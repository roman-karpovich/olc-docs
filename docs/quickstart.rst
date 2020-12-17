Quickstart
==========

1. create form

.. code-block:: python

    contact_book = Blueprint('example_contact_book', 'Contact Book example')
    contact_book.add(
        TextField('name', label='Name', required=True),
        Group(
            'users',
            TextField('full_name', required=True),
            Group(
                'phones',
                TextField('number', required=True, validations=['phone_regex']),
                TextField('type', required=False),
                required=True, repeatable=True, title='Phones',
            ),
            ChoiceField('groups', required=False, repeatable=True, options_key='groups'),
            required=False, repeatable=True, title='Users',
        ),
    )
    contact_book.metadata.options['groups'] = LocalFlatOptions(['family', 'friends', 'work', 'other'])
    contact_book.metadata.validations['phone_regex'] = RegexTextValidation(r'\d{7}')

2. configure callback url to handle form data from offline collect backend

.. code-block:: python

    blueprint = get_blueprint_for_activity_and_method(activity, method)
    validated_value = blueprint.validate(value)

    checklist = StartedChecklist.objects.create(
        author=user, monitoring_activity=activity, method=method,
        information_source=validated_value.get('information_source', {}).get('name', '')
    )

    _save_values_to_checklist(validated_value, checklist)

3. configure to keep form synchronized with offline collect

create form

.. code-block:: python

    OfflineCollect().add(data={
        "is_active": True,
        "code": blueprint.code,
        "form_title": blueprint.title,
        "form_instructions": json.dumps(blueprint.to_dict(), indent=2),
        "accessible_by": [emails],
        "api_response_url": urljoin(
            host,
            '{}?workspace={}'.format(
                reverse(
                    'field_monitoring_data_collection:activities-offline',
                    args=[self.activity.id, method.id]
                ),
                connection.tenant.schema_name or ''
            )
        )
    })


update users list

.. code-block:: python

    OfflineCollect().update(
        get_blueprint_code(self.activity, method),
        accessible_by=[emails]
    )

delete form from olc

.. code-block:: python

    OfflineCollect().delete(code)

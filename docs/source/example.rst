Example Configuration
=====================

Here is a complete example configuration from :file:`settings.py` that exercises
nearly all of the features. In this example, we're authenticating against a
global pool of users in the directory, but we have a special area set aside for
Django groups (ou=django,ou=groups,dc=example,dc=com). Remember that most of
this is optional if you just need simple authentication. Some default settings
and arguments are included for completeness.

.. code-block:: python

    import ldap
    from django_auth_ldap.config import LDAPSearch, GroupOfNamesType


    # Baseline configuration.
    AUTH_LDAP ={
        'SERVER_URI': "ldap://ldap.example.com",

        'BIND_DN': "cn=django-agent,dc=example,dc=com",
        'BIND_PASSWORD': "phlebotinum",
        'USER_SEARCH': LDAPSearch("ou=users,dc=example,dc=com",
            ldap.SCOPE_SUBTREE, "(uid=%(user)s)"),
        # or perhaps:
        # 'USER_DN_TEMPLATE': "uid=%(user)s,ou=users,dc=example,dc=com",

        # Set up the basic group parameters.
        'GROUP_SEARCH': LDAPSearch("ou=django,ou=groups,dc=example,dc=com",
            ldap.SCOPE_SUBTREE, "(objectClass=groupOfNames)"
        ),
        'GROUP_TYPE': GroupOfNamesType(name_attr="cn"),

        # Simple group restrictions
        'REQUIRE_GROUP': "cn=enabled,ou=django,ou=groups,dc=example,dc=com",
        'DENY_GROUP': "cn=disabled,ou=django,ou=groups,dc=example,dc=com",

        # Populate the Django user from the LDAP directory.
        'USER_ATTR_MAP': {
            "first_name": "givenName",
            "last_name": "sn",
            "email": "mail"
        },

        'PROFILE_ATTR_MAP': {
            "employee_number": "employeeNumber"
        },

        'USER_FLAGS_BY_GROUP': {
            "is_active": "cn=active,ou=django,ou=groups,dc=example,dc=com",
            "is_staff": "cn=staff,ou=django,ou=groups,dc=example,dc=com",
            "is_superuser": "cn=superuser,ou=django,ou=groups,dc=example,dc=com"
        },

        'PROFILE_FLAGS_BY_GROUP': {
            "is_awesome": "cn=awesome,ou=django,ou=groups,dc=example,dc=com",
        },

        # This is the default, but I like to be explicit.
        'ALWAYS_UPDATE_USER': True,

        # Use LDAP group membership to calculate group permissions.
        'FIND_GROUP_PERMS': True,

        # Cache group memberships for an hour to minimize LDAP traffic
        'CACHE_GROUPS': True,
        'GROUP_CACHE_TIMEOUT': 3600,
    }


    # Keep ModelBackend around for per-user permissions and maybe a local
    # superuser.
    AUTHENTICATION_BACKENDS = (
        'django_auth_ldap.backend.LDAPBackend',
        'django.contrib.auth.backends.ModelBackend',
    )

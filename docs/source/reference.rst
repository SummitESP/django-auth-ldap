Reference
=========

Settings
--------

Provide a dictionary as `AUTH_LDAP` with the following keys.

.. code-block:: python
    AUTH_LDAP = {
        'SERVER_URI': 'ldap://192.168.20.11',
        'CONNECTION_OPTIONS': {
            ldap.OPT_PROTOCOL_VERSION: ldap.VERSION3,
            ldap.OPT_REFERRALS: 0,
        }
    }


.. setting:: GLOBAL_OPTIONS

AUTH_LDAP_GLOBAL_OPTIONS
~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A dictionary of options to pass to ``ldap.set_option()``. Keys are
`ldap.OPT_* <http://python-ldap.org/doc/html/ldap.html#options>`_ constants.

.. note::

    Due to its global nature, this setting is set separtely and ignores the :doc:`settings name
    <multiconfig>`. Regardless of how many backends are installed, this setting is referenced once
    by its default name at the time we load the ldap module.


.. setting:: ALWAYS_UPDATE_USER

ALWAYS_UPDATE_USER
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``True``

If ``True``, the fields of a :class:`~django.contrib.auth.models.User` object
will be updated with the latest values from the LDAP directory every time the
user logs in. Otherwise the :class:`~django.contrib.auth.models.User` object
will only be populated when it is automatically created.


.. setting:: AUTHORIZE_ALL_USERS

AUTHORIZE_ALL_USERS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, :class:`~django_auth_ldap.backend.LDAPBackend` will be able furnish
permissions for any Django user, regardless of which backend authenticated it.


.. setting:: BIND_AS_AUTHENTICATING_USER

BIND_AS_AUTHENTICATING_USER
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, authentication will leave the LDAP connection bound as the
authenticating user, rather than forcing it to re-bind with the default
credentials after authentication succeeds. This may be desirable if you do not
have global credentials that are able to access the user's attributes.
django-auth-ldap never stores the user's password, so this only applies to
requests where the user is authenticated. Thus, the downside to this setting is
that LDAP results may vary based on whether the user was authenticated earlier
in the Django view, which could be surprising to code not directly concerned
with authentication.


.. setting:: BIND_DN

BIND_DN
~~~~~~~~~~~~~~~~~

Default: ``''`` (Empty string)

The distinguished name to use when binding to the LDAP server (with
:setting:`BIND_PASSWORD`). Use the empty string (the default) for an
anonymous bind. To authenticate a user, we will bind with that user's DN and
password, but for all other LDAP operations, we will be bound as the DN in this
setting. For example, if :setting:`USER_DN_TEMPLATE` is not set, we'll
use this to search for the user. If :setting:`FIND_GROUP_PERMS` is
``True``, we'll also use it to determine group membership.


.. setting:: BIND_PASSWORD

BIND_PASSWORD
~~~~~~~~~~~~~~~~~~~~~~~

Default: ``''`` (Empty string)

The password to use with :setting:`BIND_DN`.


.. setting:: CACHE_GROUPS

CACHE_GROUPS
~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, LDAP group membership will be cached using Django's cache
framework. The cache timeout can be customized with
:setting:`GROUP_CACHE_TIMEOUT`.


.. setting:: CONNECTION_OPTIONS

CONNECTION_OPTIONS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A dictionary of options to pass to each connection to the LDAP server via
``LDAPObject.set_option()``. Keys are `ldap.OPT_*
<http://python-ldap.org/doc/html/ldap.html#options>`_ constants.


.. setting:: DENY_GROUP

DENY_GROUP
~~~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

The distinguished name of a group; authentication will fail for any user
that belongs to this group.


.. setting:: FIND_GROUP_PERMS

FIND_GROUP_PERMS
~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, :class:`~django_auth_ldap.backend.LDAPBackend` will furnish group
permissions based on the LDAP groups the authenticated user belongs to.
:setting:`GROUP_SEARCH` and :setting:`GROUP_TYPE` must also be
set.


.. setting:: GROUP_CACHE_TIMEOUT

GROUP_CACHE_TIMEOUT
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

If :setting:`CACHE_GROUPS` is ``True``, this is the cache timeout for
group memberships. If ``None``, the global cache timeout will be used.


.. setting:: GROUP_SEARCH

GROUP_SEARCH
~~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

An :class:`~django_auth_ldap.config.LDAPSearch` object that finds all LDAP
groups that users might belong to. If your configuration makes any references to
LDAP groups, this and :setting:`GROUP_TYPE` must be set.


.. setting:: GROUP_TYPE

GROUP_TYPE
~~~~~~~~~~~~~~~~~~~~

Default: ``None``

An :class:`~django_auth_ldap.config.LDAPGroupType` instance describing the type
of group returned by :setting:`GROUP_SEARCH`.


.. setting:: MIRROR_GROUPS

MIRROR_GROUPS
~~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, :class:`~django_auth_ldap.backend.LDAPBackend` will mirror a user's
LDAP group membership in the Django database. Any time a user authenticates, we
will create all of his LDAP groups as Django groups and update his Django group
membership to exactly match his LDAP group membership. If the LDAP server has
nested groups, the Django database will end up with a flattened representation.


.. setting:: PERMIT_EMPTY_PASSWORD

PERMIT_EMPTY_PASSWORD
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``False`` (the default), authentication with an empty password will fail
immediately, without any LDAP communication. This is a secure default, as some
LDAP servers are configured to allow binds to succeed with no password, perhaps
at a reduced level of access. If you need to make use of this LDAP feature, you
can change this setting to ``True``.


.. setting:: PROFILE_ATTR_MAP

PROFILE_ATTR_MAP
~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A mapping from user profile field names to LDAP attribute names. A user's
profile will be populated from his LDAP attributes at login.

This is ignored in Django 1.7 and later.


.. setting:: PROFILE_FLAGS_BY_GROUP

PROFILE_FLAGS_BY_GROUP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A mapping from boolean profile field names to distinguished names of LDAP
groups. The corresponding field in a user's profile is set to ``True`` or
``False`` according to whether the user is a member of the group.

This is ignored in Django 1.7 and later.


.. setting:: REQUIRE_GROUP

REQUIRE_GROUP
~~~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

The distinguished name of a group; authentication will fail for any user that
does not belong to this group.


.. setting:: SERVER_URI

SERVER_URI
~~~~~~~~~~~~~~~~~~~~

Default: ``'ldap://localhost'``

The URI of the LDAP server. This can be any URI that is supported by your
underlying LDAP libraries.


.. setting:: START_TLS

START_TLS
~~~~~~~~~~~~~~~~~~~

Default: ``False``

If ``True``, each connection to the LDAP server will call :meth:`~ldap.LDAPObject.start_tls_s` to enable
TLS encryption over the standard LDAP port. There are a number of configuration
options that can be given to :setting:`GLOBAL_OPTIONS` that affect the
TLS connection. For example, :data:`ldap.OPT_X_TLS_REQUIRE_CERT` can be set to
:data:`ldap.OPT_X_TLS_NEVER` to disable certificate verification, perhaps to
allow self-signed certificates.


.. setting:: STATIC_GROUPS

STATIC_GROUPS
~~~~~~~~~~~~~~~~~~~

Default: ``[]``

A list of strings matching Django groups which will be ignored when using group mirroring. This
will result in the specified groups not being automatically added or removed based on values on
LDAP user. Users must explicitly be added to or removed from these groups within the Django admin
interface.


.. setting:: USER_ATTR_MAP

USER_ATTR_MAP
~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A mapping from :class:`~django.contrib.auth.models.User` field names to LDAP
attribute names. A users's :class:`~django.contrib.auth.models.User` object will
be populated from his LDAP attributes at login.


.. setting:: USER_DN_TEMPLATE

USER_DN_TEMPLATE
~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

A string template that describes any user's distinguished name based on the
username. This must contain the placeholder ``%(user)s``.


.. setting:: USER_FLAGS_BY_GROUP

USER_FLAGS_BY_GROUP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Default: ``{}``

A mapping from boolean :class:`~django.contrib.auth.models.User` field names to
distinguished names of LDAP groups. The corresponding field is set to ``True``
or ``False`` according to whether the user is a member of the group.


.. setting:: USER_SEARCH

USER_SEARCH
~~~~~~~~~~~~~~~~~~~~~

Default: ``None``

An :class:`~django_auth_ldap.config.LDAPSearch` object that will locate a user
in the directory. The filter parameter should contain the placeholder
``%(user)s`` for the username. It must return exactly one result for
authentication to succeed.


Module Properties
-----------------

.. module:: django_auth_ldap

.. data:: version

    The library's current version number as a 3-tuple.

.. data:: version_string

    The library's current version number as a string.


Configuration
-------------

.. module:: django_auth_ldap.config

.. class:: LDAPSearch

    .. method:: __init__(base_dn, scope, filterstr='(objectClass=*)')

        * ``base_dn``: The distinguished name of the search base.
        * ``scope``: One of ``ldap.SCOPE_*``.
        * ``filterstr``: An optional filter string (e.g. '(objectClass=person)').
          In order to be valid, ``filterstr`` must be enclosed in parentheses.

.. class:: LDAPSearchUnion

    .. versionadded:: 1.1

    .. method:: __init__(\*searches)

        * ``searches``: Zero or more LDAPSearch objects. The result of the
          overall search is the union (by DN) of the results of the underlying
          searches. The precedence of the underlying results and the ordering of
          the final results are both undefined.

.. class:: LDAPGroupType

    The base class for objects that will determine group membership for various
    LDAP grouping mechanisms. Implementations are provided for common group
    types or you can write your own. See the source code for subclassing notes.

    .. method:: __init__(name_attr='cn')

        By default, LDAP groups will be mapped to Django groups by taking the
        first value of the cn attribute. You can specify a different attribute
        with ``name_attr``.

.. class:: PosixGroupType

    A concrete subclass of :class:`~django_auth_ldap.config.LDAPGroupType` that
    handles the ``posixGroup`` object class. This checks for both primary group
    and group membership.

    .. method:: __init__(name_attr='cn')

.. class:: NISGroupType

    A concrete subclass of :class:`~django_auth_ldap.config.LDAPGroupType` that
    handles the ``nisNetgroup`` object class.

    .. method:: __init__(name_attr='cn')

.. class:: MemberDNGroupType

    A concrete subclass of
    :class:`~django_auth_ldap.config.LDAPGroupType` that handles grouping
    mechanisms wherein the group object contains a list of its member DNs.

    .. method:: __init__(member_attr, name_attr='cn')

        * ``member_attr``: The attribute on the group object that contains a
          list of member DNs. 'member' and 'uniqueMember' are common examples.


.. class:: NestedMemberDNGroupType

    Similar to :class:`~django_auth_ldap.config.MemberDNGroupType`, except this
    allows groups to contain other groups as members. Group hierarchies will be
    traversed to determine membership.

    .. method:: __init__(member_attr, name_attr='cn')

        As above.


.. class:: GroupOfNamesType

    A concrete subclass of :class:`~django_auth_ldap.config.MemberDNGroupType`
    that handles the ``groupOfNames`` object class. Equivalent to
    ``MemberDNGroupType('member')``.

    .. method:: __init__(name_attr='cn')


.. class:: NestedGroupOfNamesType

    A concrete subclass of
    :class:`~django_auth_ldap.config.NestedMemberDNGroupType` that handles the
    ``groupOfNames`` object class. Equivalent to
    ``NestedMemberDNGroupType('member')``.

    .. method:: __init__(name_attr='cn')


.. class:: GroupOfUniqueNamesType

    A concrete subclass of :class:`~django_auth_ldap.config.MemberDNGroupType`
    that handles the ``groupOfUniqueNames`` object class. Equivalent to
    ``MemberDNGroupType('uniqueMember')``.

    .. method:: __init__(name_attr='cn')


.. class:: NestedGroupOfUniqueNamesType

    A concrete subclass of
    :class:`~django_auth_ldap.config.NestedMemberDNGroupType` that handles the
    ``groupOfUniqueNames`` object class. Equivalent to
    ``NestedMemberDNGroupType('uniqueMember')``.

    .. method:: __init__(name_attr='cn')


.. class:: ActiveDirectoryGroupType

    A concrete subclass of :class:`~django_auth_ldap.config.MemberDNGroupType`
    that handles Active Directory groups. Equivalent to
    ``MemberDNGroupType('member')``.

    .. method:: __init__(name_attr='cn')


.. class:: NestedActiveDirectoryGroupType

    A concrete subclass of
    :class:`~django_auth_ldap.config.NestedMemberDNGroupType` that handles
    Active Directory groups. Equivalent to
    ``NestedMemberDNGroupType('member')``.

    .. method:: __init__(name_attr='cn')


.. class:: OrganizationalRoleGroupType

    A concrete subclass of :class:`~django_auth_ldap.config.MemberDNGroupType`
    that handles the ``organizationalRole`` object class. Equivalent to
    ``MemberDNGroupType('roleOccupant')``.

    .. method:: __init__(name_attr='cn')


.. class:: NestedOrganizationalRoleGroupType

    A concrete subclass of
    :class:`~django_auth_ldap.config.NestedMemberDNGroupType` that handles the
    ``organizationalRole`` object class. Equivalent to
    ``NestedMemberDNGroupType('roleOccupant')``.

    .. method:: __init__(name_attr='cn')


Backend
-------

.. module:: django_auth_ldap.backend

.. data:: populate_user

    This is a Django signal that is sent when clients should perform additional
    customization of a :class:`~django.contrib.auth.models.User` object. It is
    sent after a user has been authenticated and the backend has finished
    populating it, and just before it is saved. The client may take this
    opportunity to populate additional model fields, perhaps based on
    ``ldap_user.attrs``. This signal has two keyword arguments: ``user`` is the
    :class:`~django.contrib.auth.models.User` object and ``ldap_user`` is the
    same as ``user.ldap_user``. The sender is the
    :class:`~django_auth_ldap.backend.LDAPBackend` class.

.. data:: populate_user_profile

    Like :data:`~django_auth_ldap.backend.populate_user`, but sent for the user
    profile object. This will only be sent if the user has an existing profile.
    As with :data:`~django_auth_ldap.backend.populate_user`, it is sent after the
    backend has finished setting properties and before the object is saved. This
    signal has two keyword arguments: ``profile`` is the user profile object and
    ``ldap_user`` is the same as ``user.ldap_user``. The sender is the
    :class:`~django_auth_ldap.backend.LDAPBackend` class.

    This is not sent in Django 1.7 and later.

.. data:: ldap_error

    This is a Django signal that is sent when we receive an
    :exc:`ldap.LDAPError` exception. The signal has two keyword arguments:
    ``'context'`` is one of ``'authenticate'``, ``'get_group_permissions'``, or
    ``'populate_user'``, indicating which API was being called when the
    exception was caught; and ``'exception'`` is the :exc:`~ldap.LDAPError`
    object itself. The sender is the
    :class:`~django_auth_ldap.backend.LDAPBackend` class.

.. class:: LDAPBackend

    :class:`~django_auth_ldap.backend.LDAPBackend` has one method that may be
    called directly and several that may be overridden in subclasses.

    .. data:: settings_name

        The name of the settings dict in Django settings. By default, this is
        ``'AUTH_LDAP'``, but subclasses can override this. When different
        subclasses use different names and dicts, they can both be installed and
        operate independently.

    .. data:: default_settings

        A dictionary of default settings. This is empty in
        :class:`~django_auth_ldap.backend.LDAPBackend`, but subclasses can
        populate this with values that will override the built-in defaults.

    .. data:: ldap_user_class

        The class which will be used to instaniate `LDAPUser` instances. By default this is
        `~django_auth_ldap.backend.LDAPUser`, but can be set to any subclass of `LDAPUser` to
        customize user objects.

    .. data:: ldap_groups_class

        The class to use when instanciating `LDAPUserGroup` instances. The default is
        `~django_auth_ldap.backend.LDAPUserGroups`, but can be set to any `LDAPUserGroups` subclass
        to customize group lookup and interaction.

    .. method:: populate_user(username)

        Populates the Django user for the given LDAP username. This connects to
        the LDAP directory with the default credentials and attempts to populate
        the indicated Django user as if they had just logged in.
        :setting:`ALWAYS_UPDATE_USER` is ignored (assumed ``True``).

    .. method:: get_user_model(self)

        Returns the user model that
        :meth:`~django_auth_ldap.backend.LDAPBackend.get_or_create_user` will
        instantiate. In Django 1.5, custom user models will be respected; in
        earlier versions, the model defaults to
        :class:`django.contrib.auth.models.User`. Subclasses would most likely
        override this in order to substitute a :ref:`proxy model
        <proxy-models>`.

    .. method:: get_or_create_user(self, username, ldap_user)

        Given a username and an LDAP user object, this must return a valid
        Django user model instance. The ``username`` argument has already been
        passed through
        :meth:`~django_auth_ldap.backend.LDAPBackend.ldap_to_django_username`.
        You can get information about the LDAP user via ``ldap_user.dn`` and
        ``ldap_user.attrs``. The return value must be the same as
        :meth:`~django.db.models.query.QuerySet.get_or_create`: an (instance,
        created) two-tuple.

        The default implementation calls ``<model>.objects.get_or_create()``,
        using a case-insensitive query and creating new users with lowercase
        usernames. The user model is obtained from
        :meth:`~django_auth_ldap.backend.LDAPBackend.get_user_model`. A subclass
        may override this to associate LDAP users to Django users any way it
        likes.

    .. method:: ldap_to_django_username(username)

        Returns a valid Django username based on the given LDAP username (which
        is what the user enters). By default, ``username`` is returned
        unchanged. This can be overridden by subclasses.

    .. method:: django_to_ldap_username(username)

        The inverse of
        :meth:`~django_auth_ldap.backend.LDAPBackend.ldap_to_django_username`.
        If this is not symmetrical to
        :meth:`~django_auth_ldap.backend.LDAPBackend.ldap_to_django_username`,
        the behavior is undefined.

.. class:: LDAPUser

    :class:`~django_auth_ldap.backend.LDAPUser` can be customized, but due to it's complexity and
    the rarity of needing to customize, I would recommend reading the source thoroughly.

.. class:: LDAPUserGroups

    :class:`~django_auth_ldap.backend.LDAPUserGroups` can be customized, but due to it's complexity
    and interconnectedness with `LDAPUser` I would recommend reading the source thoroughly.

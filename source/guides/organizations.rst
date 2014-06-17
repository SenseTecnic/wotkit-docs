========================================
Organizations for Privacy and Visibility
========================================

WoTKit *organizations* are used to group sensors owned by an organization, and restrict the visibility of sensors and groups to other users in the system who are members of the same organization.

Organization members may have different roles: OWNER, ADMIN, and MEMBER.

* MEMBER - a member can view private sensors and groups in the organization.  They cannot add sensors or groups to the organization, modify organization membership or roles, or delete the organization.
* ADMIN - an admin is a MEMBER who can add or remove members, and add or remove sensors and groups to an organization.  They have read and write priveledges to sensors and groups.
* OWNER - an owner is an ADMIN who owns the organization and can delete the organization.

Private Sensors and Groups
--------------------------

When group or sensor is marked as *private*, it can only be viewed by organization members.

Note: when a group contains a mix of private and public sensors from different organizations, some sensors in a group may not be visible to all users in a given organization.

Creating an Organization
------------------------

When you create an organization, you are the owner of that organization.  As owner, you can add members, and assign roles.

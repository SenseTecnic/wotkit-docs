========================================
Organizations for Privacy and Visibility
========================================

WoTKit *organizations* are used to group sensors owned by an organization, and restrict the visibility of sensors and groups to other users in the system who are members of the same organization.

Organization members may have different roles: OWNER, ADMIN, and MEMBER.

* OWNER - an owner can manage members of an organization, view sensors and groups in the organization, send data to sensors, and receive commands from actuators (read and write priveledges).  Only an owner can delete an organization.
* ADMIN - a member administrator can do everything an owner can, other than delete the organization.
* MEMBER - a member can view sensors and groups.  They cannot modify organization membership or roles, or delete the organization.

Private Sensors and Groups
--------------------------

When group or sensor is marked as *private*, it can only be viewed by organization members.

Note: when a group contains a mix of private and public sensors from different organizations, some sensors in a group may not be visible to all users in a given (single) organization.

Creating an Organization
------------------------

When you create an organization, you are the owner of that organization.

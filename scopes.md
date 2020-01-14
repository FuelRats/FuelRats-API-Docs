# Scopes / Permissions (WIP)

Fuel Rats permissions are formatted as resource.read/write/delete(.me).
* The 'resource' is always the plural name of a resource type, such as "rats" or "rescues".  
* The "read" identifier gives access to viewing records of a type of resource.
* The "write" identifier gives access to creating and updating a type of resource. 

A permission may be suffixed with ".me" to restrict the permission to only be effective for records that are associated with the user. (For example the user's rescues, the user's rat, the user's account, the user's nicknames).   

Requesting scopes that the user themselves does not have access to (for example deleting rescues when the user does not have permission to do so) will not result in an error during the oauth grant request, but attempting to make operations that require it will result in a permission error. Should the user be granted this permission in the future, your application will as well.

## Permission Scopes

#### Rescues

Permission | Description | Granted To
---------|----------|---------
rescues.read | Read any rescue | Verified Users
rescues.write | Create modify, and delete any rescue | Overseer, Moderator, Admin, Techrat
rescues.read.me | Read your own rescues | Verified Users
rescues.write.me | Modify your own rescues | Verifised Users

"Your own rescues" is defined as any rescue in which your rat is assigned to. 
   
#### Rats (CMDRs)

Permission | Description | Granted To
---------|----------|---------
rats.read | Read any rat | Verified Users
rats.write | Create modify, and delete any rat | Overseer, Moderator, Admin, Techrat
rats.read.me | Read your own rat | Verified Users
rats.write.me | Create, modify and delete your own rat | Verified Users

"Your own rat" is defined as any rat that is associated with your Fuel Rats account, deleting your own rats is only actually possible if they do not have any associated rescues.

#### Users

Permission | Description | Granted To
---------|----------|---------
users.read | Read any user | Moderator, Admin, Techrat
users.write | Modify and delete any user | Moderator, Admin, Techrat
users.read.me | Read your own user | Verified Users
users.write.me | Modify your own user | Verified Users

Anyone's user object is actually visible to anyone, but sensitive fields such as email is hidden for those without explicit read permission.

#### Nicknames

Permission | Description | Granted To
---------|----------|---------
nicknames.read | Read any nickname | Moderator, Admin, Techrat
nicknames.write | Create, modify or delete any nickname | Moderator, Admin, Techrat
nicknames.read.me | Read your own nicknames | Verified Users
nicknames.write.me | Create, modify or delete your own nicknames | Verified Users

Anyone's nicknames is actually visible to anyone, but some sensitive fields are hidden for those without explicit read permission.

#### OAuth Clients

Permission | Description | Granted To
---------|----------|---------
clients.read | Read any OAuth client | Verified Users
clients.write | Create, modify, or delete any OAuth client | Admin, Techrat
clients.read.me | Read your own OAuth client | Verified Users
clients.write.me | Create, modify or delete your own OAuth clients | Developer, Moderator, Admin, Techrat

### Ships

Permission | Description | Granted To
---------|----------|---------
ships.read | Read any ship | Verified Users
ships.write | Create, modify, or delete any ship | Admin, Moderator, Techrat
ships.write.me | Create, modify or delete your own ships | Verified Users



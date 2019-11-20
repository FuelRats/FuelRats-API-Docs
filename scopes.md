# Scopes / Permissions (WIP)

Fuel Rats permissions are formatted as resource.read/write/delete(.me).
* The 'resource' is always the singular name of a resource type, such as "rat" or "rescue".  
* The "read" identifier gives access to viewing records of a type of resource.
* The "write" identifier gives access to creating and updating a type of resource.
* The "delete" identifier gievs access to deleting a type of resource.  

A permission may be suffixed with ".me" to restrict the permission to only be effective for records that are associated with the user. (For example the user's rescues, the user's rat, the user's account, the user's nicknames).   

Requesting scopes that the user themselves does not have access to (for example deleting rescues when the user does not have permission to do so) will not result in an error during the oauth grant request, but attempting to make operations that require it will result in a permission error. Should the user be granted this permission in the future, your application will as well.

## Permission Scopes

#### Rescues

Permission | Description | Granted To
---------|----------|---------
rescue.read | Read any rescue | Verified Users
rescue.write | Create and modify any rescue | Overseer, Moderator, Admin, Techrat
rescue.delete | Delete any rescue | Overseer, Moderator, Admin, Techrat
rescue.read.me | Read your own rescues | Verified Users
rescue.write.me | Modify your own rescues | Verifised Users

"Your own rescues" is defined as any rescue in which your rat is assigned to. 
   
#### Rats (CMDRs)

Permission | Description | Granted To
---------|----------|---------
rat.read | Read any rat | Verified Users
rat.write | Create and modify any rat | Overseer, Moderator, Admin, Techrat
rat.delete | Delete any rat | Moderator, Admin, Techrat
rat.read.me | Read your own rat | Verified Users
rat.write.me | Modify your own rat | Verified Users
rat.delete.me | Delete your own rat | Verified Users

"Your own rat" is defined as any rat that is associated with your Fuel Rats account, deleting your own rats is only actually possible if they do not have any associated rescues.

#### Users

Permission | Description | Granted To
---------|----------|---------
user.read | Read any user | Moderator, Admin, Techrat
user.write | Modify any user | Moderator, Admin, Techrat
user.delete | Delete any user | Moderator, Admin, Techrat
user.read.me | Read your own user | Verified Users
user.write.me | Modify your own user | Verified Users

Anyone's user object is actually visible to anyone, but sensitive fields such as email is hidden for those without explicit read permission.

#### Nicknames

Permission | Description | Granted To
---------|----------|---------
nickname.read | Read any nickname | Moderator, Admin, Techrat
nickname.write | Create or Modify any nickname | Moderator, Admin, Techrat
nickname.delete | Delete any nickname | Moderator, Admin, Techrat
nickname.read.me | Read your own nicknames | Verified Users
nickname.write.me | Create and modify your own nicknames | Verified Users
nickname.delete.me | Delete your own nicknames | Verified Users

Anyone's nicknames is actually visible to anyone, but some sensitive fields are hidden for those without explicit read permission.

#### OAuth Clients

Permission | Description | Granted To
---------|----------|---------
client.read | Read any OAuth client | Verified Users
client.write | Create and modify any OAuth client | Admin, Techrat
client.delete | Delete any OAuth client | Admin, Techrat
client.read.me | Read your own OAuth client | Verified Users
client.write.me | Create and modify your own OAuth clients | Developer, Moderator, Admin, Techrat
client.delete.me | Delete your own OAuth clients | Developer, Moderator, Admin, Techrat


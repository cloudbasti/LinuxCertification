finding files: 

1. find all files with size smaller 1kb, owner can read and write, group only read and others no permissions
2. find all files group can either read or execute, others cannot read, owner can only execute.

copy, move, remove: 

1. copy all files that belong to a certain user to another directory.


user management and group management

ALWAYS VERIFY for each step that it worked

1. create user bob, alice and mary
2. bob joins group magicthegathering and sports, alice joins sports and kindergarden and magic, and mary joins magic and kindergarden (first named 
group is always primary group)
3. create users john and johnny
4. add john and johnny to kindergarden and sports
5. set a password for bob and for mary
6. set account expiration dates for 2 accounts
7. use usermod and groupmod utility to change things now
8. use passwd and gpasswd to change things now
9. set user limits and group limits for the users on the system
10. add user to ACL
11. Set User Priviliges

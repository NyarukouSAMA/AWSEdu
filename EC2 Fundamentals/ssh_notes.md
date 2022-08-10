For easier usage of ssh it will be better to configure user configuration file. For this:
- in user directory create folder .ssh
- create config file (it should have any extension)
- add some properties, for example **IdentityFile**

To solve protection issue with .pem file:
- in Linux chmod 400 filename.pem
- in Windows: open properties -> "Security" tab -> Advanced -> Make yourself owner -> Disable inheritance and delete all -> Add yourself with full permissions
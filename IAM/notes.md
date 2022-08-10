# IAM

- **IAM** is an Identity and Access Management. It's a global service.
- **Root account** - created by default and shouldn't be used or shared normally.
- **Users** - created with the help of IAM. They are people from your organization and with the help of IAM they can be organized into groups with different access rights. Groups represent roles of the people inside.
- Groups can only contain users, but not other groups.
- Users don't have to be inside any group, *but this is not best practice*. Users can also be inside multiple groups (when they have multiple roles in your team).

# IAM Permissions

- Users or Groups can be assigned JSON document called policies.
- **Policy** is a representation of permissions for different services inside AWS.
- Best practice for applying permissions is the **least privilege principle** (don't give more permissions then user needs)

# User creating

To create user we:
- will go to IAM
- open "User" tab
- click "Add user"
- assignee user to some group or to some permission policy directly (the best practice is to use groups)
- then set up credentials
- assignee some tags

If we don't have any group we should make anyone with one or more assigned policies.
We can choose as from the variety of default policies but also from custom policies, that we can create ourself.
For group we can use inline assignment for custom policy (it means, we create policy locally for this group only) or attach some policy, created in section "Policies". In this case better way to manage permissions is to create custom policies and attach them to groups instead of using inline assignment.

Commonly the best way of permissions management is creating policies, groups and users separately. When we need to add or remove some permission to user the best ways of solving are adding it to a new group or adding some policy to the group, which this user belongs to, or updating the policy itself, depends on needs. In some cases adding of the inline policy can be acceptable, but in this case you should remember about the concrete user with the inline policy. The same is truly for groups. If there is a need to attach some inline policy, then it should be documented.

For root user on the dashboard we can change the account alias from default (it's not convenient because it's nine symbols number) to some custom. `With the help of this alias we can open sign in form for our AWS service account.

# IAM Policies Structure
```
{
    "Version": policy language version, always include "2012-10-17",
    "Id": an identifier for the policy (optional),
    "Statement": one or more individual statements (required)
    [
        {
            "Sid": an identifier for the statement (optional),
            "Effect": "Allow" or "Deny",
            "Principle": account/user/role to which this policy applied to (optional)
            "Action": list of actions this policy allows or denies,
            "Resources": list of resources to which actions applied to,
            "Condition": conditions for when this policy is in effect (optional)
        }
    ]
}
```

The example of the policy you could watch in the AdministratorAccess.json

# IAM Defense mechanisms

In IAM we have two defense mechanisms.
First is a password policy. In AWS you can setup a password policy:
- set minimum password length
- require specific characters (both uppercase and lowercase letters, special symbols, numbers)
- allow all IAM users to change their passwords
- require users to change their password after some time (expiration policy)
- prevent a password re-use

Second is called the multi factor authentication (MFA). It can be reached with the help of **password you know and security device you owr**.

Second part of the MFA can be reached with the help of some application, where you link your account to your device (usually your smartphone), and you use your device to generate a security token. And when you are logging in you should generate the token and use it in authentication form.

This part of MFA could also be reached with the help of special USB-key device. Token records on usb-key device when in process of manufacturing. And then this usb-device is owned by someone and should not be handed over to some one else.

MFA should be used at least to protect the root account.

MFA device options in AWS:
- virtual MFA device (for example: Google authenticator OR Authy)
- universal 2nd Factor (U2F) Security Key (for example: YubiKey (3-rd party), that in fact usb-key device)
- hardware key fob MFA Device (for example: Gemalto's key (3-rd party))
- hardware key fob MFA Device for AWS GovCloud (US) (for example: SurePassID's key (3-rd party))

# Password policy and MFA settings

To set up password policy:
- open IAM as root or full admin user
- go to "Account settings" tab
- there we have "Change password policy" button

To set up MFA for your user:
- in left corner of AWS console open user menu
- choose security credential
- open section MFA
- when start creating MFA, choose the type of MFA and follow instruction

To set up MFA for other users:
- go to IAM "Users" tab
- choose user you are interested in
- in section "Sign-in credentials" click "Manage"
- choose type of MFA and follow instruction

# Access key

There are three options to access AWS:
- AWS management console (user's interface through browser) - protected by password and (it's optional) MFA
- AWS command line interface (AWS CLI) - protected by access key
- AWS software dev kit (AWS SDK) - also protected by access key

Beside that there is also an opportunity to use AWS CLI through browser. For this we should open AWS management console and there we can run AWS CLI in browser. But it available not everywhere. 

Access key generated through the AWS Console. To generate it:
- open IAM service's "User" tab
- choose user interested in
- open "Security credential" tab
- push "Create access key" button in "Access key" section

Key will be generated after it. It consists from access key id and secret access key. Secret key will be shown only once, in generating process. You also can download .csv file with secret key (to import it or to send to some user (if you are admin)).

# IAM Roles

Some AWS services could be needed in performing some actions with other AWS services. It means that this services should have permissions for these actions. To assignee permissions in AWS used **IAM Roles**.

# IAM Security Tools

Those are tools for reviewing different information from IAM about permissions setting. There we have two tools:
- IAM Credential Report (account-level) - a report, that lists all your account's users and the status of their various credentials; to access it: open IAM, the go to "Credential report" and then download .csv file with interesting information
- IAM Access Advisor (user-level) - it shows service permissions granted to a user and when those services were last accessed, it can be used to revise your policies; to access it: in IAM open user you are interested in and then open "Access Advisor" tab

# IAM Guideline and Best Practices

- Don't use the root account except for AWS account setup
- One physical user = one AWS user
- Assign users to groups and assign permissions to groups
- Strong password policy
- MFA (at least for root account and accounts with admin's permissions)
- Access Key for CLI\SDK
- From time to time make the permission's audit (IAM Credential Report and Access Advisor)
- Don't share IAM users & Access Keys
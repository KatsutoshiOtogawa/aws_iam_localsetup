# aws_iam_localsetup
you create new User added appropriate permission in Aws IAM.
you create OS user *aws_console*. *aws_console* is writtent by using you create new user.
And, you aws cli was install and every user is enable to use aws cli.
```
# Firstly, for sake of simplicity, you set Environment aws accesskey, aws password, aws secrect accesskey, and etc.
su - *aws_console* -c 'echo export AWS_PASSWORD=*AWS_PASSWORD*  >> $HOME/.bash_profile'
su - *aws_console* -c 'echo export AWS_ACCESS_KEY=*AWS_ACCESS_KEY* >> $HOME/.bash_profile'
su - *aws_console* -c 'echo export AWS_SECRET_ACCESS_KEY=*AWS_SECRET_ACCESS_KEY* >> $HOME/.bash_profile'
su - *aws_console* -c 'echo export AWS_CONSOLE_LOGIN_URL=*AWS_CONSOLE_LOGIN_URL* >> $HOME/.bash_profile'
```

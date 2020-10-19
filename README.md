# aws_local_OSusersetup
you create new User added appropriate permission in Aws IAM.

you create OS user *aws_console*. *aws_console* is writtent by using you create new user.

And, you aws cli was install and every user is enable to use aws cli.
## mac OS
```
yousetupuser=[yousetupuser]
# Update login shell for macOS.
su - $yousetupuser -c "chsh -s /bin/bash"

# Firstly, for sake of simplicity, you set Environment aws accesskey, aws password, aws secrect accesskey, and etc.
# AWS_PASSWORD and AWS_CONSOLE_LOGIN_URL are optionnal.These are setten to be easy ordinary time.
cat << END >> aws_credential.py
import csv
import os
with open(os.path.join(os.environ["HOME"],'new_user_credentials.csv')) as f:
    data = csv.DictReader(f)
    for row in data:
        with open(os.path.join(os.environ["HOME"],'.bash_profile'),'a') as bash_profile:
            bash_profile.write('aws configure set aws_access_key_id {}\n'.format(row['Access key ID']))
            bash_profile.write('aws configure set aws_secret_access_key {}\n'.format(row['Secret access key']))     
            bash_profile.write('aws configure set region {}\n'.format('ap-northeast-1'))
            bash_profile.write('aws configure set format {}\n'.format('json'))
            bash_profile.write('export AWS_PASSWORD={}\n'.format(row['Password']))
            bash_profile.write('export AWS_CONSOLE_LOGIN_URL={}\n'.format(row['Console login link']))

END

sudo mv aws_credential.py new_user_credentials.csv /Users/$yousetupuser
sudo chown $yousetupuser /Users/$yousetupuser/aws_credential.py
sudo chown $yousetupuser /Users/$yousetupuser/new_user_credentials.csv

# execute python script and set aws credential.
su - $yousetupuser -c "python3 \$HOME/aws_credential.py"
```

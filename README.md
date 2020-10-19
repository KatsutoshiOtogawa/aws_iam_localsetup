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
            bash_profile.write('export AWS_INITIAL_PASSWORD={}\n'.format(row['Password']))
            bash_profile.write('export AWS_CONSOLE_LOGIN_URL={}\n'.format(row['Console login link']))

END

sudo mv aws_credential.py new_user_credentials.csv /Users/$yousetupuser
sudo chown $yousetupuser /Users/$yousetupuser/aws_credential.py
sudo chown $yousetupuser /Users/$yousetupuser/new_user_credentials.csv

# execute python script and set aws credential.
su - $yousetupuser -c "python3 \$HOME/aws_credential.py"
```
## You Usually use OS youser 

```
export yousetupuser=[yousetupuser]
python3 << END
import csv
import os
with open(os.path.join(os.environ["HOME"],'new_user_credentials.csv')) as f:
    data = csv.DictReader(f)
    for row in data:
        with open(os.path.join(os.environ["HOME"],'.bash_profile'),'a') as bash_profile:
            bash_profile.write('# switch aws user\n')
            bash_profile.write('function {} {{\n'.format(os.environ['yousetupuser']))
            bash_profile.write('    aws configure set aws_access_key_id {}\n'.format(row['Access key ID']))
            bash_profile.write('    aws configure set aws_secret_access_key {}\n'.format(row['Secret access key']))     
            bash_profile.write('    aws configure set region {}\n'.format('ap-northeast-1'))
            bash_profile.write('    aws configure set format {}\n'.format('json'))
            bash_profile.write('    export AWS_INITIAL_PASSWORD={}\n'.format(row['Password']))
            bash_profile.write('    export AWS_CONSOLE_LOGIN_URL={}\n'.format(row['Console login link']))
            # }はエスケープしなくて良い。
            bash_profile.write('}\n')

END

# you want to swich aws user, you type ${yousetupuser} and Enter.
${yousetupuser}
```

# using aws iam
```
yousetupuser=[yousetupuser]
export POLICYARN=$(aws iam list-policies --query 'Policies[?PolicyName==`PowerUserAccess`].{ARN:Arn}' --output text)

# create aws credential user
aws iam create-user --user-name $yousetupuser
aws iam attach-user-policy --user-name $yousetupuser --policy-arn $POLICYARN
aws_initial_password=$(mkpasswd -l 16)
aws iam create-login-profile --user-name $yousetupuser --password $aws_initial_password --password-reset-required
aws iam create-access-key --user-name $yousetupuser >> $yousetupuser-access-key.json
aws_access_key_id=$(cat $yousetupuser-access-key.json | jq -r .AccessKey.AccessKeyId)
aws_secret_access_key=$(cat $yousetupuser-access-key.json | jq -r .AccessKey.SecretAccessKey)

cat << END >> ~/.bash_profile
# switch aws user
function $yousetupuser () {
    aws configure set aws_access_key_id $aws_access_key_id
    aws configure set aws_secret_access_key $aws_secret_access_key
    aws configure set region 'ap-northeast-1'
    aws configure set format json
    export AWS_INITIAL_PASSWORD=$aws_initial_password
    export AWS_CONSOLE_LOGIN_URL=$aws_initial_password
}
END
```

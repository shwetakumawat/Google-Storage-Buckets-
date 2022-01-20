A script to enumerate Google Storage buckets, determine what access you have to them, and determine if they can be privilege escalated.

- This script accepts GCP user/service account credentials and a keyword.
- Then, a list of permutations will be generated from that keyword which will then be used to scan for the existence of Google Storage buckets with those names.
- If credentials are supplied, the majority of enumeration will still be performed while unauthenticated, but for any bucket that is discovered via unauthenticated enumeration, it will attempt to enumerate the bucket permissions using the TestIamPermissions API with the supplied credentials. This will help find buckets that are accessible while authenticated, but not while unauthenticated.
- Regardless if credentials are supplied or not, the script will then try to enumerate the bucket permissions using the TestIamPermissions API while unauthenticated. This means that if you don't enter credentials, you will only be shown the privileges an unauthenticated user has, but if you do enter credentials, you will see what access authenticated users have compared to unauthenticated users.
- **WARNING:** If credentials are supplied, your username can be disclosed in the access logs of any buckets you discover.

## TL;DR Summary
- Given a keyword, this script enumerates Google Storage buckets based on a number of permutations generated from the keyword.
- Then, any discovered bucket will be output.
- Then, any permissions that you are granted (if any) to any discovered bucket will be output.
- Then the script will check those privileges for privilege escalation (storage.buckets.setIamPolicy) and will output anything interesting (such as publicly listable, publicly writable, authenticated listable, privilege escalation, etc).

## Requirements

- Linux/OS X
	- Windows only works for unauthenticated scans. Something is wrong with how the script uses the subprocess module in that it fails when using an authenticated Google client.
- Python3
- Pip3

## Installation

1. `git clone https://github.com/RhinoSecurityLabs/GCPBucketBrute.git`
2. `cd GCPBucketBrute/`
3. `pip3 install -r requirements.txt` or `python3 -m pip install -r requirements.txt`

## Usage

First, determine the type of authentication you want to use for enumeration between a user account, service account, or unauthenticated. If you are using a service account, provide the file path to the private key via the `-f`/`--service-account-credential-file-path` argument. If you are using a user account, don't provide an authentication argument. You will then be prompted to enter the access token of your user account for accessing the GCP APIs. If you want to scan completely unauthenticated, pass the `-u`/`--unauthenticated` argument to hide authentication prompts.

- Scan for buckets using the keyword "test" while completely unauthenticated:
```
python3 gcpbucketbrute.py -k test -u
```

- Scan for buckets using the keyword "test" while authenticating with a service account (private key stored at ../sa-priv-key.pem), outputting results to out.txt in the current directory:
```
python3 gcpbucketbrute.py -k test -f ../sa-priv-key.pem -o ./out.txt
```

- Scan for buckets using the keyword "test", using a user account access token, running with 10 subprocesses instead of 5:
```
python3 gcpbucketbrute.py -k test -s 10
```


## Database
_Tables_: chatopssubscriptions, chatopsInstallations  
# List of workflows:
## subscribe
## unsubscribe
## subscribe list
Lists the repositories and accounts that users have subscribed to in the given channel.

_command_: `@github subscribe list`

Users are not required to be signed in to view the list of subscriptions.

## Decision:
***Request:***
We pass in the _channelId_, _teamId_, _clientAppId_ from the context. We are querying the **chatopssubscriptions** table with these id's. 

***Reply:***
We get the _ghInstallationId_ from **chatopsinstallations** table (_chatOpsInstallationId_ is a foreign key in the **chatopssubscriptions** table).

We get the _ghArtifactId_, _ghArtifactType_ from the chatopssubscriptions table.

_ghInstallationId_ is used to get authorization to the github client which will be used to make the api calls to get details about the artifact (This will tell us if the app has been uninstalled from the repo/account after the subscription).

**The following details are passed to the GithubApi:**
   * if _ghArtifactType_ is 'Repo':  _ghArtifactId_ is passed, it contains the repository id.
   * if _ghArtifactType_is 'Account': _ghArtifactId_ is passed, it contains the user id.


if the authorization is successful, using the GithubAPI We will get the details about each subscription.

Results are displayed as hyperlinks. Text will be the full_name of the repository or the github account name. The hyperlinks will redirect to the repository or the github user account.

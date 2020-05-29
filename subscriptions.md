## Database
_Tables_: chatopssubscription, chatopsinstallation, chatopsgithubuser 
# List of commands:
## subscribe
## unsubscribe
## subscribe list
_command_: `@github subscribe list`

Lists the repositories and accounts that that user has subscribed to in the given channel.
Users are not required to connect their GitHub account on Teams to view the list of subscriptions.

## Flow:
When the user submits subscribe list command:

1) All the arguments passed after the word _list_ are ignored.
2) _chatopssubscription_ and _chatopsinstallation_ tables are queried

     <ins>***Request:***</ins>
     * _channelId_, _teamId_, _clientAppId_
     
     <ins>***Reply:***</ins>
     * _ghInstallationId_ from **chatopsinstallation** table (_chatOpsInstallationId_ is a foreign key in the **chatopssubscription** table).
     * _ghArtifactId_, _ghArtifactType_ from the chatopssubscriptions table.
     
3) Using the _ghInstallationId_ we get the [authentication to the github client as an installation](https://developer.github.com/apps/building-github-apps/authenticating-with-github-apps/#authenticating-as-an-installation). Then we will get the details about the artifact based on the artifact id, such as repository name, account name, github url etc..

   **The following details are passed to the GithubAPI:**
   * if _ghArtifactType_ is 'Repo': repository id.
   * if _ghArtifactType_is 'Account': user id.
   
4) If we are able to retreive the details about the atifact, we further do the [authentication as a GitHub app](https://developer.github.com/apps/building-github-apps/authenticating-with-github-apps/#authenticating-as-a-github-app). At this point, we check if our Teams App on GitHub can still access the artifact (this check will tell us if the access to a repository has been revoked after a subscription was created in the Teams channel). This will eliminate such repositories from showing up in the list.

5) Results are displayed as hyperlinks. Text will be the full_name of the repository or the github account name. The hyperlinks will redirect to the repository or the github user account.

## Database
_Tables_: chatopssubscription, chatopsinstallation, chatopsgithubuser 
# List of commands:
## subscribe
## unsubscribe
_command_: `@GitHub unsubscribe owner/repository`, `@GitHub unsubscribe owner`

Users can use these commands to unsubscribe from notifications for a repository or to unsubscribe from notifications for an organization. All signed in users can use this command and if not signed in are prompted to do so.

## Flow:
When the user submits the unsubscribe command

1) If repository or owner is not passed after the unsubscribe command an error message is shown Eg., `@GitHub unsubscribe`
2) Then we check if the user is signed-in, if the user is not signed in, user will be prompted with a sign in card.
3) If the user is signed-in, we are going to check if the GitHub resource (owner or owner/repository) passed in the commnad exist on GitHub.
   * if a URL is passed in the command, we verify the validity of the URL using the [npm url module](https://www.npmjs.com/package/url). We are not going to process a http: protocol. If the owner or owner/repository could not be extracted from the URL or if the URL passed is not a GitHub URL, an error message will be shown.
   * if the arguments passed doesn't match the owner or owner/repository criteria, an error message will be shown.
   


## subscribe list
_command_: `@GitHub subscribe list`

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

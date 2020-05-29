## Database
_Tables_: chatopssubscription, chatopsinstallation, chatopsgithubuser 
# List of commands:
## subscribe
_command_: `@GitHub subscribe owner/repository`, `@GitHub subscribe owner`

User can use these commands to subscribe to notifications for a repository or subscirbe for all repositories for this owner that our Teams app on GitHub can access. This command subscribes to all default features. If the channel is already subscribed the user gets the notification ‘You're already subscribed to org or repo name”. All signed in users can use this command and if not signed in are prompted to do so. 

## Flow:
When the user submits the subscribe command

1) If a repository or owner is not passed after the subscribe command, an error message is shown Eg., `@GitHub subscribe`
2) Then we check if the user is signed-in, if the user is not signed in, user will be prompted with a sign in card.
3) If the user is signed-in, we are going to check if the GitHub resource (owner or owner/repository) passed in the commnad is in valid format and that if the account exists on GitHub.
   * Firstly, if a URL is passed in the command, we verify the validity of the URL using the [npm url module](https://www.npmjs.com/package/url). If the owner or owner/repository could not be extracted from the URL or if the URL passed is not a GitHub URL, an error message will be shown. We are not going to process a http: protocol, error message will be shown in this case too.
   * if the arguments passed doesn't match the owner or owner/repository format, an error message will be shown.
   * Once this check is done, we are going to use to check if an user exists [GetByUserName](https://octokit.github.io/rest.js/v17#users-get-by-username)
   * If the user could not be found, an error message will be shown
4) Once the validation is done, we are going to use https://github.com/github/slack/blob/master/src/lib/chatops/models/chatops-installations.ts module to see if our Teams app is installed on the GitHub account passed and can access the repository passed (if any). If installation cannot be found or if the app cannot access the repo, install app card is shown to install Teams Github app.Yoou can click on the install app button, to modify the access to your repository as well. We are getting the _chatOpsInstallationId_ from this step.
    * If you are trying to subscribe to an organization. Some one with owner privileges can only install the app in the organization.
5) If Our Teams app on GitHub is installed and can access the resource passed in the command, we are going to get details about the resource from the resource name using the same same API's as in step 3 eg.,(account id, repository id etc..). This resource id will be treated as _ghArtifactId_ in our **chatopssubscripiton** table
6) We are going to query **chatopssubscription** table

    <ins>***Request:***</ins>
    * _ghArtifactId_, _chatOpsInstallationId_, _channelId_, _teamId_, _clientAppId_
7) If a record is found from the previous criteria, it indicates that the subscription was already created for the GitHub resource. Same message will be displayed to the user.
8) If no record is found in step 6, We will be creating a new row in the _chatopssubscription_ table and the user is shown the sucess message for the newly created subscription. If there is a failure while inserting into the database, we will show the same message.

    <ins>***Request:***</ins>
    * _channelId, chatOpsUsersId, ghArtifactId, ghArtifactName, chatOpsInstallationsId, teamId, ghArtifactType (repo or account),    clientAppId, settings_
    
## unsubscribe
_command_: `@GitHub unsubscribe owner/repository`, `@GitHub unsubscribe owner`

Users can use these commands to unsubscribe from notifications for a repository or to unsubscribe from notifications for an organization. All signed in users can use this command and if not signed in are prompted to do so.   
   
## Flow:
When the user submits the unsubscribe command

1) If a repository or owner is not passed after the unsubscribe command, an error message is shown Eg., `@GitHub unsubscribe`
2) Then we check if the user is signed-in, if the user is not signed in, user will be prompted with a sign in card.
3) If the user is signed-in, we are going to check if the GitHub resource (owner or owner/repository) passed in the commnad is in valid format and that if the account exists on GitHub.
   * Firstly, if a URL is passed in the command, we verify the validity of the URL using the [npm url module](https://www.npmjs.com/package/url). If the owner or owner/repository could not be extracted from the URL or if the URL passed is not a GitHub URL, an error message will be shown. We are not going to process a http: protocol, error message will be shown in this case too.
   * if the arguments passed doesn't match the owner or owner/repository format, an error message will be shown.
   * Once this check is done, we are going to use to check if an user exists [GetByUserName](https://octokit.github.io/rest.js/v17#users-get-by-username)
   * If the user or the repository could not be found, an error message will be shown.
4) Once the validation is done, we are going to use the API's from above step to get the resource id form GitHub.
5) Then we query the **chatopssubscription** table to see if an entry exists for our resource

  <ins>***Request:***</ins>
  * ghArtifactId, channelId, teamId, clientAppId
  
6) If an entry is found in the, we delete the entry from the table and the same message will be shown to the user.
8) If an entry is not found, same message is shown to the user.
   
## subscribe list
_command_: `@GitHub subscribe list`

Lists the repositories and accounts that that user has subscribed to in the given channel.
Users are not required to connect their GitHub account on Teams to view the list of subscriptions.

## Flow:
When the user submits subscribe list command:

1) All the arguments passed after the word _list_ are ignored.
2) **chatopssubscription** and **chatopsinstallation** tables are queried

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

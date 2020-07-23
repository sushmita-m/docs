
Users can customize the different types of notifications received from subscriptions.
Settings can be configured using the following commands

## Supported Features
_issues, pulls, commits, comments_ are enabled by default.
_reviews, +label:_ are not supported by default.

## Configuring features:
* `@github subscribe owner/repo [features]`
* `@github unsubscribe owner/repo [features]`

1) User can chooose to mention any of the supported features along with the subscribe command
2) Multiple features can be subscribed/unsubscribed at once, they can be either comma seperated or space seperated
2) Subscription is created for the features enabled by default if features are not passed with the subscribe command
3) For any new subscription, if a non-default feature is passed in the command, 
subscription will be created for the additional features along with features supported by default.
4) User can unsubscribe to a feature, using the unsubscribe feature command `@github unsubscribe owner/repo [features]


## Filtering with label
* `@github subscribe owner/repo +label:docs`
* `@github unsubscribe owner/repo +label:docs`

1) If a label is subscribed to, issues, reviews and comments will be filtered based on the label
2) User can only subscribe to one label at a time
3) Label can be updated by running the subscribe command with the new label
4) The value passed for the label is case sensitive, the case of the label should exaclty match the one set in GitHub

##Valid Labels
Comma is not supported
spaces should be mentioned in quotes _"release docs", 'command docs'
Most of the special characters are supported _"release:GA", "branch:release/docs"


##Flow:
The flow is similar to the subscribe flow.
The values of the features are stored as a json in the settings field of the chatopssubscription.
Any feature subscribed/unsubscribed by the user will be alloted a value of true/false in the json.
The value of label is stored in the required_field key with the label in an array as value in the JSON.





rq

1) Users can choose to mention any of the supported features along with the subscribe command.
2) Multiple features can be subscribed/unsubscribed to, they can be either comma separated or space separated.
2) If features are not passed with the subscribe command, subscription is created for the features enabled by default.
3) For any new subscription, if a non-default feature is passed in the command, subscription will be created for the additional features along with features supported by default.
4) User can unsubscribe to a feature, using the unsubscribe feature command `@github unsubscribe owner/repo [features]


## Filtering with label
* `@github subscribe owner/repo +label:docs`
* `@github unsubscribe owner/repo +label:docs`

1) If a label is subscribed to, **issues, reviews and comments** will be filtered based on the label.
2) User can only subscribe to one label for a subscription.
3) Label can be updated by running the subscribe command with the new label.
4) The value passed for the label is case sensitive, the case of the label should exactly match the one set in GitHub

## Valid Labels
1) Comma is not supported in a label.
2) Spaces should be mentioned in quotes _+label:"release docs", +label:'command docs'_
3) Most of the special characters are supported _+label:"release:GA", +label:"branch:release/docs"


## Flow:
1) The flow is similar to the subscribe flow.
2) The values of the features are stored as a json in the settings field of the chatopssubscriptions table.
3) Any feature subscribed/unsubscribed by the user will be alotTed a value of true/false in the JSON.
4) The value of label is stored in the required_field key with the label value in an array in the JSON.
456




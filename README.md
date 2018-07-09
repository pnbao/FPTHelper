# FPTHelper

## Requirements

* AndroidStudio 3.0
* Android API 25 or greater

## Using the FPTHelper

1. Import the FPTHelper project into Android Studio.
   - From the Welcome screen, click on "Import project".
   - Browse to the FPTHelper directory and press OK.
   - Accept the messages about adding Gradle to the project.
   - If the SDK reports some missing Android SDK packages (like Build Tools or the Android API package), follow the instructions to install them.

2. Import the libraries :
   - Gradle will take care of downloading these dependencies for you.

3. This FPTHelper requires Cognito to authorize to Amazon Lex to post content.  Use Amazon Cognito to create a new identity pool:
	3.1. In the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/), press the `Manage Federated Identities` button and on the resulting page press the `Create new identity pool` button.
	
	3.2. Give your identity pool a name and ensure that `Enable access to unauthenticated identities` under the `Unauthenticated identities` section is checked.  This allows the FPTHelper application to assume the unauthenticated role associated with this identity pool.  Press the `Create Pool` button to create your identity pool.

		**Important**: see note below on unauthenticated user access.

	3.3. As part of creating the identity pool, Cognito will setup two roles in [Identity and Access Management (IAM)](https://console.aws.amazon.com/iam/home#roles).  These will be named something similar to: `Cognito_PoolNameAuth_Role` and `Cognito_PoolNameUnauth_Role`.  You can view them by pressing the `View Details` button.  Now press the `Allow` button to create the roles.
	3.4. Save the `Identity pool ID` value that shows up in red in the "Getting started with Amazon Cognito" page, it should look similar to: `us-east-1:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and note the region that is being used.  These will be used in the application code later.
	3.5. Now we will attach a policy to the unauthenticated role which has permissions to access the required Amazon Lex API.  This is done by attaching an IAM Policy to the unauthenticated role in the [IAM Console](https://console.aws.amazon.com/iam/home#roles).  First, search for the unauth role that you created in step 3 above (named something similar to `Cognito_PoolNameUnauth_Role`) and select its hyperlink.  In the resulting "Summary" page press the `Attach Policy` button in the "Permissions" tab.
	3.6. Search for "lex" and check the box next to the policy named `AmazonLexFullAccess` and then press the `Attach Policy` button.  This policy allows the application to perform all operations on the Amazon Lex service.

		More information on AWS IAM roles and policies can be found [here](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html).  More information on Amazon Lex policies can be found [here](http://docs.aws.amazon.com/lex/latest/dg/access-control-managing-permissions.html).

		**Note**: to keep this example simple it makes use of unauthenticated users in the identity pool.  This can be used for getting started and prototypes but unauthenticated users should typically only be given read-only permissions in production applications.  More information on Cognito identity pools including the Cognito developer guide can be found [here](http://aws.amazon.com/cognito/).

4. Open the FPTHelper project.

5. Open `res/values/strings.xml` and update the values for Cognito Identity Pool ID (from the value you saved above), Cognito region for Cognito Identity Pool Id (for example us-east-1), Lex region, your Amazon Lex Bot name and the Bot Alias (usually $LATEST).

6. Build and run the FPTHelper app.

7. Select Text Demo or Voice Demo as appropriate, and try out some of the FPTHelper utterance you have configured on the Amazon Lex console.

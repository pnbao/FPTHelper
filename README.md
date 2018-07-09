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



.. _add-aws-mobile-sdk-basic-setup:

Set Up Backend
===================

#. `Sign up for the AWS Free Tier. <https://aws.amazon.com/free/>`__

#. `Create a Mobile Hub project <https://console.aws.amazon.com/mobilehub/>`__ by signing into the console. The Mobile Hub console provides a single location for managing and monitoring your app's cloud resources.

   To integrate existing AWS resources using the SDK directly, without Mobile Hub, see :doc:`Setup  Options for Android <how-to-android-sdk-setup>` or :doc:`Setup  Options for iOS <how-to-ios-sdk-setup>`.

#. Name your project, check the box to allow Mobile Hub to administer resources for you and then choose :guilabel:`Add`.

      #. Choose :guilabel:`Android` as your platform and then choose Next.

         .. image:: images/wizard-createproject-platform-android.png
            :scale: 75

      #. Choose the :guilabel:`Download Cloud Config` and then choose :guilabel:`Next`.

         The :file:`awsconfiguration.json` file you download contains the configuration of backend resources that |AMH| enabled in your project. Analytics cloud services are enabled for your app by default.

         .. image:: images/wizard-createproject-backendsetup-android.png
            :scale: 75


      #. Add the backend service configuration file to app.

         In the Project Navigator, right-click your app's :file:`res` folder, and then choose :guilabel:`New > Directory`. Type :userinput:`raw` as the directory name and then choose :guilabel:`OK`.

            .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
               :scale: 100
               :alt: Image of creating a raw directory in Android Studio.

            .. only:: pdf

               .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
                  :scale: 50

            .. only:: kindle

               .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
                  :scale: 75

         From the location where configuration file, :file:`awsconfiguration.json`, was downloaded in a previous step, drag it into the :file:`res/raw` folder.  Android gives a resource ID to any arbitrary file placed in this folder, making it easy to reference in the app.

         .. list-table::
            :widths: 1 6

            * - **Remember**

              - Every time you create or update a feature in your |AMH| project, download and integrate a new version of your :file:`awsconfiguration.json` into each app in the project that will use the update.

      Your backend is now configured. Follow the next steps at :ref:`Connect to Your Backend <add-aws-mobile-sdk-connect-to-your-backend>`.

    

.. _add-aws-mobile-sdk-connect-to-backend:

Connect to Backend
=======================

      #. Prerequisites

         * `Install Android Studio <https://developer.android.com/studio/index.html#downloads>`__ version 2.33 or higher.

         * Install Android SDK v7.11 (Nougat), API level 25.

      #. Your :file:`AndroidManifest.xml` must contain:

         .. code-block:: xml

             <uses-permission android:name="android.permission.INTERNET"/>
             <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

      #. Add dependencies to your :file:`app/build.gradle`, then choose :guilabel:`Sync Now` in the upper right of Android Studio. This libraries enable basic AWS functions, like credentials, and analytics.

         .. code-block:: java

             dependencies {
                 implementation ('com.amazonaws:aws-android-sdk-mobile-client:2.6.+@aar') { transitive = true }
             }

      #. Add the following code to the :code:`onCreate` method of your main or startup activity. :code:`AWSMobileClient` is a singleton that establishes your connection to |AWS| and acts as an interface for your services.

         .. code-block:: java

            import com.amazonaws.mobile.client.AWSMobileClient;

              public class YourMainActivity extends Activity {
                @Override
                protected void onCreate(Bundle savedInstanceState) {
                    super.onCreate(savedInstanceState);

                    AWSMobileClient.getInstance().initialize(this, new AWSStartupHandler() {
                        @Override
                        public void onComplete(AWSStartupResult awsStartupResult) {
                            Log.d("YourMainActivity", "AWSMobileClient is instantiated and you are connected to AWS!");
                        }
                    }).execute();

                    // More onCreate code ...
                }
              }

         .. list-table::
            :widths: 1 6

            * - What does this do?

              - When :code:`AWSMobileClient` is initialized, it constructs the :code:`AWSCredentialsProvider` and :code:`AWSConfiguration` objects which, in turn, are used when creating other SDK clients. The client then makes a `Sigv4 signed <https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html>`__ network call to `Amazon Cognito Federated Identities <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html>`__ to retrieve AWS credentials that provide the user access to your backend resources. When the network interaction succeeds, the :code:`onComplete` method of the :code:`AWSStartUpHandler` is called.

      Your app is now set up to interact with the AWS services you configured in your Mobile Hub project!

      Choose the run icon (|play|) in Android Studio to build your app and run it on your device/emulator. Look for :code:`Welcome to AWS!` in your Android Logcat output (choose :guilabel:`View > Tool Windows > Logcat`).

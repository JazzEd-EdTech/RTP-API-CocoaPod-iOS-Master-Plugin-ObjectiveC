# Master CocoaPod Plugin for adding Sequencing.com's Real-Time Personalization technology to iOS apps coded in Objective-C
=========================================
This Master CocoaPod Plugin can be used to quickly add Real-Time Personalization to your app. This Master Plugin contains a customizable, end-to-end plug-n-play solution that quickly adds all necessary code (OAuth2, File Selector and App Chain coding) to your app.

Once this Master Plugin is added to your app all you'll need to do is:

1. add your [OAuth2 secret](https://sequencing.com/developer-center/new-app-oauth-secret)
2. add one or more [App Chain numbers](https://sequencing.com/app-chains/)
3. configure your app based on each [app chain's possible responses](https://sequencing.com/app-chains/)

To code Real-Time Personalization technology into apps, developers may [register for a free account](https://sequencing.com/user/register/) at Sequencing.com. App development with RTP is always free.

**The Master Plugin is also available in the following languages:**
* Objective-C (CocoaPod plugin) <-- this repo
* [Swift (CocoaPod plugin)](https://github.com/SequencingDOTcom/CocoaPods-iOS-Master-Plugin-Swift)
* [Android (Maven plugin)](https://github.com/SequencingDOTcom/Maven-Android-Master-Plugin-Java)
* [Java (Maven plugin)](https://github.com/SequencingDOTcom/Maven-Android-Master-Plugin-Java)


Contents
=========================================
* Implementation
* Master CocoaPod Plugin install
* OAuth CocoaPod Plugin integration
* File Selector CocoaPod Plugin integration
* AppChains CocoaPod Plugin integration
* Resources
* Maintainers
* Contribute


Implementation
======================================
To implement this Master Plugin for your app:

1) [Register](https://sequencing.com/user/register/) for a free account

2) Add this Master Plugin to your app

3) [Generate an OAuth2 secret](https://sequencing.com/api-secret-generator) and insert the secret into the plugin

4) Add one or more [App Chain numbers](https://sequencing.com/app-chains/). The App Chain will provide genetic-based information you can use to personalize your app.

5) Configure your app based on each [app chain's possible responses](https://sequencing.com/app-chains/)


Master CocoaPod Plugin install
======================================
* see [CocoaPods guides](https://guides.cocoapods.org/using/using-cocoapods.html)
* create Podfile in your project directory: ```$ pod init```
* specify "sequencing-master-plugin-api-objc" pod parameters in Podfile: 

	```pod 'sequencing-master-plugin-api-objc', '~> 1.0.0'```

* install the dependency in your project: ```$ pod install```
* always open the Xcode workspace instead of the project file: ```$ open *.xcworkspace```
* as a result you'll have 3 CocoaPod plugins installed: OAuth, Files Selector and AppChains


OAuth CocoaPod Plugin integration
======================================

* create View Controllers, e.g. for Login screen and for Start screen
	
* in your LoginViewController class:
	
	* add imports
		```
		#import "SQOAuth.h"
		#import "SQToken.h"
		```
		
	* for authorization you need to specify your application parameters in NSString format (BEFORE using authorization method) 
		```
		static NSString *const CLIENT_ID	 = @"your CLIENT_ID here";
		static NSString *const CLIENT_SECRET = @"your CLIENT_SECRET here";
		static NSString *const REDIRECT_URI	 = @"REDIRECT_URI here";
		static NSString *const SCOPE         = @"SCOPE here";
		```    
			
	* register these parameters into OAuth module instance
		```
		[[SQOAuth sharedInstance] registrateApplicationParametersCliendID:CLIENT_ID 
									ClientSecret:CLIENT_SECRET 
									RedirectUri:REDIRECT_URI 
									Scope:SCOPE];
		```
		
	* add import for protocol
		```
		#import "SQAuthorizationProtocol.h"
		```
			
	* subscribe your class for this protocol
		```
		<SQAuthorizationProtocol>
		```
	
	* subscribe your class as delegate for such protocol
		```
		[[SQOAuth sharedInstance] setAuthorizationDelegate:self];
		```
		
	* add methods for SQAuthorizationProtocol
		```
		- (void)userIsSuccessfullyAuthorized:(SQToken *)token {
			dispatch_async(dispatch_get_main_queue(), ^{
				// your code is here for successful user authorization
			});
		}

		- (void)userIsNotAuthorized {
			dispatch_async(dispatch_get_main_queue(), ^{
				// your code is here for unsuccessful user authorization
			});
		}
		```
		
	* you can authorize your user now (e.g. via "login" button). For authorization you can use ```authorizeUser``` method. You can get access via shared instance of SQOAuth class)
		```
		[[SQOAuth sharedInstance] authorizeUser];
		```
			
		Related method from SQAuthorizationProtocol will be called as a result
		
	* example of Login button (you can use ```@"button_signin_black"``` image that is included into the Pod within ```AuthImages.xcassets```)
		```
		// set up login button
   		UIButton *loginButton = [UIButton buttonWithType:UIButtonTypeCustom];
   		[loginButton setImage:[UIImage imageNamed:@"button_signin_black"] forState:UIControlStateNormal];
   		[loginButton addTarget:self action:@selector(loginButtonPressed) forControlEvents:UIControlEventTouchUpInside];
   		[loginButton sizeToFit];
   		[loginButton setTranslatesAutoresizingMaskIntoConstraints:NO];
   		[self.view addSubview:loginButton];
   
   		// adding constraints for login button
   		NSLayoutConstraint *xCenter = [NSLayoutConstraint constraintWithItem:loginButton 
   																attribute:NSLayoutAttributeCenterX 
   																relatedBy:NSLayoutRelationEqual 
   																toItem:self.view 
   																attribute:NSLayoutAttributeCenterX 
   																multiplier:1 constant:0];
    															
   		NSLayoutConstraint *yCenter = [NSLayoutConstraint constraintWithItem:loginButton
   																attribute:NSLayoutAttributeCenterY
   																relatedBy:NSLayoutRelationEqual
   																toItem:self.view
   																attribute:NSLayoutAttributeCenterY
   																multiplier:1
   																constant:0];
   		[self.view addConstraint:xCenter];
   		[self.view addConstraint:yCenter];
   		```
    	
   	* example of ```loginButtonPressed``` method 
   		```
   		- (void)loginButtonPressed {
   			[[SQOAuth sharedInstance] authorizeUser];
   		}
   		```   		
		
	* add segue in Storyboard from LoginViewController to MainViewController with identifier ```GOTO_MAIN_SCREEN```
		
	* add constant for segue id
		```static NSString *const GOTO_MAIN_SCREEN_SEGUE_ID = @"GOTO_MAIN_SCREEN";```
		
	* example of navigation methods when user is authorized
		```
		- (void)userIsSuccessfullyAuthorized:(SQToken *)token {
			dispatch_async(dispatch_get_main_queue(), ^{
				[self performSegueWithIdentifier:GOTO_MAIN_SCREEN_SEGUE_ID sender:token];
			});
		}
			
		- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
			if ([segue.identifier isEqual:GOTO_MAIN_SCREEN_SEGUE_ID]) {
				StartViewController *startVC = segue.destinationViewController;
				[startVC setToken:sender];
			}
		}
		```
		
* in your StartViewController class:
		
	* add imports
		```
		#import "SQOAuth.h"
		#import "SQToken.h"
		```
		
	* add import for protocol
		```
		#import "SQTokenRefreshProtocol.h"
		```
		
	* subscribe your class for these protocols
		```
		<SQTokenRefreshProtocol>
		```
		
	* subscribe your class as delegate for such protocols
		```
		[[SQOAuth sharedInstance] setRefreshTokenDelegate:self];
		```
		
	* add method for SQTokenRefreshProtocol - it is called when token is refreshed
		```
		- (void)tokenIsRefreshed:(SQToken *)updatedToken {
			// your code is here to handle refreshed token
		}
		```
		
	* in method ```userIsSuccessfullyAuthorized``` you'll receive SQToken object, that contains following 5 properties with clear titles for usage:
		```	
		NSString *accessToken
		NSDate   *expirationDate
		NSString *tokenType
		NSString *scope
		NSString *refreshToken
		```
	
	* in method ```tokenIsRefreshed``` you'll receive updated token with the same object model.
	
		DO NOT OVERRIDE ```refresh_token``` property for ```token``` object - it comes as ```nil``` after refresh token request
	
	* for your extra needs you can always get access directly to the up-to-day token object which is stored in ```SQAuthResult``` class via ```token``` property
		```
		[[SQAuthResult sharedInstance] token];
		```


File Selector CocoaPod Plugin integration
======================================

* Set up file selector UI

	* add "Storyboard Reference" in your Main.storyboard
		* select added Storyboard Reference
		* open Utilities > Atributes inspector
		* select ```TabbarFileSelector``` in Storyboard dropdown
		
	* add segue from your ViewController to created Storyboard Reference
		* open Utilities > Atributes inspector
		* name this segue as ```GET_FILES``` in Identifier field
		* set Kind as ```Modal```
		
	* add ```TabbarFileSelector.storyboard``` file into your project Bundle Resources
		* Build Phases > Copy Bundle Resources > add your ```TabbarFileSelector``` storyboard using the icon "+"

* Subscribe for file selector protocol

	* add file selector protocol import in your class (e.g. StartViewController class) were you getting and handling file selector:
		```
		#import "SQFileSelectorProtocol.h"
		```	
		
	* subscribe your class to file selector protocol: 
		```
		<SQFileSelectorProtocol>
		```
		
	* add import: 
		```
		#import "SQFilesAPI.h"
		```
		
	* subscribe your class as handler/delegate for selected file in file selector: 
		```
		[[SQFilesAPI sharedInstance] setFileSelectedHandler:self];
		```
		
	* implement "handleFileSelected" method from protocol
		```
		- (void)handleFileSelected:(NSDictionary *)file {
			// your code here
		}
		```

* Use file selector 

	* set up some button for getting/viewing files for logged in user, and specify delegate method for this button
	
	* specify UI segue name constant
		```
		static NSString *const FILES_CONTROLLER_SEGUE_ID = @"GET_FILES";
		```	
		
	* you can load/get files, list of my files and list of sample files, via ```withToken: loadFiles:``` method (via ```SQFilesAPI``` class with shared instance init access).
	
		pay attention, you need to pass on the String value of ```token.accessToken``` object as a parameter for this method:
		```
		[[SQFilesAPI sharedInstance] withToken:self.token.accessToken loadFiles:^(BOOL success) {
			// your code here
		}];
		```
		
		```withToken: loadFiles:``` method will return a BOOL value with YES if files were successfully loaded or NO if there were any problem. You need to manage this in your code
		
	* if files were loaded successfully you can open/show File Selector now in UI. You can do it by calling file selector view via ```performSegueWithIdentifier``` method:
		```
		[self performSegueWithIdentifier:FILES_CONTROLLER_SEGUE_ID sender:@0];
		```
		
		note: this code will work only if you already set up the reference to TabbarFileSelector.storyboard in your storyboard
		
	* example of ```Select File``` button
		```
		// set up select file button
    	UIButton *selectFileButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    	[selectFileButton setTitle:@"Select file" forState:UIControlStateNormal];
    	[selectFileButton addTarget:self action:@selector(getFiles:) forControlEvents:UIControlEventTouchUpInside];
    	[selectFileButton sizeToFit];
    	[selectFileButton setTranslatesAutoresizingMaskIntoConstraints:NO];
    	[self.view addSubview:selectFileButton];
    
    	// adding constraints for select file
    	NSLayoutConstraint *xCenter = [NSLayoutConstraint constraintWithItem:selectFileButton
    															attribute:NSLayoutAttributeCenterX
    															relatedBy:NSLayoutRelationEqual
    															toItem:self.view
    															attribute:NSLayoutAttributeCenterX
    															multiplier:1
    															constant:0];
    															
    	NSLayoutConstraint *yCenter = [NSLayoutConstraint constraintWithItem:selectFileButton
    															attribute:NSLayoutAttributeCenterY
    															relatedBy:NSLayoutRelationEqual
    															toItem:self.view
    															attribute:NSLayoutAttributeCenterY
    															multiplier:1
    															constant:0];
    	[self.view addConstraint:xCenter];
    	[self.view addConstraint:yCenter];
		```
	
	* example of ```getFiles``` method
		```
		- (void)getFiles:(UIButton *)sender {	
			[[SQFilesAPI sharedInstance] withToken:self.token.accessToken loadFiles:^(BOOL success) {
				dispatch_async(kMainQueue, ^{
					if (success) {
						// redirect user to view with tab bar with related files displayed (with subcategories)
						[self performSegueWithIdentifier:FILES_CONTROLLER_SEGUE_ID sender:@0];
					
					} else {
						[self showAlertWithMessage:@"Can't load files"];
					}
				});
			}];
		}
		```
		
	* selected file will already appear as a parameter in ```handleFileSelected:``` method from ```SQFileSelectorProtocol``` protocol. In this method you can handle selected file
	
	* each file is a NSDictionary object with following keys and values:
		```
		DateAdded:     "string value"
		Ext:           "string value"
		FileCategory:  "string value"
		FileSubType:   "string value"
		FileType:      "string value"
		FriendlyDesc1: "string value"
		FriendlyDesc2: "string value"
		Id:            "string value"
		Name:          "string value"
		Population:    "string value"
		Sex:           "string value"
    	```


AppChains CocoaPod Plugin integration
======================================
There are no strict configurations that have to be performed.

Just drop the source files for an app chain into your project to add Real-Time Personalization to your app.

Code snippets below contain the following three placeholders. Please make sure to replace each of the placeholders with real values:
* ```<your token>``` 
 * replace with the oAuth2 secret obtained from your [Sequencing.com account](https://sequencing.com/api-secret-generator)
  * The code snippet for enabling Sequencing.com's oAuth2 authentication for your app can be found in the [oAuth2 code and demo repo](https://github.com/SequencingDOTcom/oAuth2-code-and-demo)

* ```<chain id>``` 
 * replace with the App Chain ID obtained from the list of [App Chains](https://sequencing.com/app-chains)

* ```<file id>``` 
 * replace with the file ID selected by the user while using your app
  * The code snippet for enabling Sequencing.com's File Selector for your app can be found in the [File Selector code repo](https://github.com/SequencingDOTcom/File-Selector-code)


AppChains Objective-C API overview

Method  | Purpose | Arguments | Description
------------- | ------------- | ------------- | -------------
`-(instancetype)initWithToken:(NSString *)token` | Constructor | **token** - security token provided by sequencing.com | 
`-(instancetype)initWithToken:(NSString *)token withHostName:(NSString *)hostName`  | Constructor | **token** - security token provided by sequencing.com <br> **hostName** - API server hostname. api.sequencing.com by default | Constructor used for creating AppChains class instance in case reporting API is needed and where security token is required
`- (void)getReportWithRemoteMethodName:(NSString *)remoteMethodName             withApplicationMethodName:(NSString *)applicationMethodName                      withDatasourceId:(NSString *)datasourceId withSuccessBlock:(void (^)(Report *result))success withFailureBlock:(void (^)(NSError *error))failure`  | Reporting API | **remoteMethodName** - REST endpoint name, use "StartApp" <br> **applicationMethodName** - name of data processing routine <br> **datasourceId** - input data identifier <br> <br> **success** - callback executed on success operation<br> **failure** - callback executed on operation failure

Adding code to the project:
* Add AppChains.h, AppChains.m into your source folder and import AppChains in your Objective-C source file (```#import "AppChains.h"```).

After that you can start utilizing Reporting API

```objectivec
[appChains getReportWithRemoteMethodName:@"StartApp"
               withApplicationMethodName:@"<chain id>"
                        withDatasourceId:@"<file id>"
                        withSuccessBlock:^(Report *result) {
                               NSArray *arr = [result getResults];
                               for (Result *obj in arr) {
                                    ResultValue *frv = [obj getValue];

                                    if ([frv getType] == kResultTypeFile) {
                                        [(FileResultValue *)frv saveToLocation:@"/tmp/"];
                                    }
                                }
                            } withFailureBlock:^(NSError *error) {
                                NSLog(@"Error occured: %@", [error description]);
                            }];
```


Resources
======================================
* [App chains](https://sequencing.com/app-chains)
* [File selector code](https://github.com/SequencingDOTcom/File-Selector-code)
* [Generate OAuth2 secret](https://sequencing.com/developer-center/new-app-oauth-secret)
* [Developer Center](https://sequencing.com/developer-center)
* [Developer Documentation](https://sequencing.com/developer-documentation/)


Maintainers
======================================
This repo is actively maintained by [Sequencing.com](https://sequencing.com/). Email the Sequencing.com bioinformatics team at gittaca@sequencing.com if you require any more information or just to say hola.


Contribute
======================================
We encourage you to passionately fork us. If interested in updating the master branch, please send us a pull request. If the changes contribute positively, we'll let it ride.

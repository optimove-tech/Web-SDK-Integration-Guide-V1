
 - [Basic Setup](#basic-setup) 
	 - [Request a Web SDK from Optimove ](#request-sdk) 
	 - [Remove legacy code (if any) ](#remove-legacy-code) 
	 - [Add Web SDK script to your website / tag manager](#add-code) 
	 - [Stitching Website Visitors to Registered Customer IDs ](#link-visit-customer) 
	- [Tracking Page Visits](#track-visits) 
	- [Reporting/Updating User Email Addresses](#record-email) 
	- [Registering the User ID and User Email at the Same Time](#record-user-email) 
 - [Advanced Setup](#advanced-setup)
 	- [Reporting Custom Events](#custom-events) 
	- [How to Report an Custom Event using server-side programming](#server-side-events) 

----------

# <a id="basic-setup"></a>Basic Setup
You can also watch our [webinar](https://academy.optimove.com/customer-retention/webinar-optimove-web-sdk-basic-setup) for more information on how to integrate the basic setup.

Use the basic setup of the Web SDK in order to:

-   Implement [Track & Trigger](https://docs.optimove.com/track-and-trigger/)
-   Implement [Google Display Network](https://github.com/optimove-tech/GDN) execution channel

### 1. <a id="request-sdk"> </a> Request a Web SDK from Optimove

Contact your Customer Success Manager (CSM) or Optimove point of contact to request your Web SDK configuration details in order to get started.

### 2. <a id="remove-legacy-code"> </a> Remove any legacy tracking scripts from the website

During the Web SDK onboarding, if you have been using any previous Optimove scripts (Optitrack/Realtime) for the purposes of tracking events or performing Google cookie matching, please remove them, as the Web SDK and legacy code cannot run simultaneously. If you are uncertain which version you have implemented, please contact Optimove Product Integration team.
>**Note:** 
> You should continue sending events in production to the legacy Optimove tracking script while implementing this SDK on your staging/testing environment.

### 3. <a id="add-code"> </a> Add the Optimove Web SDK script to your website

The following code snippet must be added to every page in your website, either by adding it into the relevant site template files/code or using a website tag manager (such as Google Tag Manager [example code snippet](https://github.com/optimove-tech/Web-SDK-Integration-Guide/blob/master/GTM-CustomHTML-Code-Snippet.html)). This code will load and initialize the SDK.
```javascript
 <script src="https://sdk-cdn.optimove.net/websdk/sdk-vSDKVersion.js"></script>
    <script type="text/javascript">
       optimoveSDK.initialize("TenantToken", "ConfigVersion", function(){
          // events may be called here
       }, "info");
 </script>
```
>**Note:** 
> Remember to replace **TenantToken**, **ConfigVersion** and **SDKVersion** with the actual details that you receive from Optimove’s Integration Team.

### 4. <a id="link-visit-customer"></a>Stitching Website Visitors to Registered Customer IDs

In order for all event reporting and realtime functions to be properly associated with the correct individual customer, the **setUserId**(SDK_ID) function must be called whenever a customer initially registers (or, alternatively, the [registerUser](https://github.com/optimove-tech/Web-SDK-Integration-Guide#record-user-email)() function) or logs into the website. 

Example usage:
```javascript
// The SDK_ID refers to the unique/primary customer ID used by your website to identify registered customers/users. 
var SDK_ID = '123456';

// Only call the setUserID() if registered / identified customers **is not** empty, null, unidentified. 
If(SDK_ID != 'undefined') {
	optimoveSDK.API.setUserId(SDK_ID);
}
```
>**Note:** 
> - The **SDK_ID** must match your Customer ID (CID) your are sending Optimove on a daily basis  and is is also used to identify individual customer records within your Optimove customer database.
> - For  extra security purposes, you can also send the SDK_ID encrypted. Please follow the steps in “[Reporting encrypted CustomerIDs](https://github.com/optimove-tech/Reporting-Encrypted-CustomerID)".

### 5. <a id="track-visits"></a>Tracking Page Visits

In order to track page visits, call the setPageVisit() function on every page of the website to ensure that accurate user counts and session time metrics are collected. 
```javascript
// PageURL: The page URL (string, required)
// PageTitle: The page title (string, required)
// PageTitle: The page title (string, required)
optimoveSDK.API.setPageVisit(PageURL, PageTitle, PageCategory);
```

Example usage:
```javascript
// general shared function to captures page visits
function updateSDKPageVisit (PageURL, PageTitle, PageCategory) {
	// SDK function
	optimoveSDK.API.setPageVisit(PageURL, PageTitle, PageCategory);
}

// variables indicating the page info
var PageURL = 'https://www.myshop.com/clothes/unisex/shirts?item=123456';
var PageTitle = 'Some Shirt Name';
var PageCategory = 'Clothes Unisex Shirts';

// calling the shared function with the relavant variables
updateSDKPageVisit (PageURL, PageTitle, PageCategory);
```
>**Note:** 
> - Every page view is recorded, including repeated visits to the same page.


### 6. <a id="record-email"></a>Reporting / Updating User Email Addresses

Whenever the website captures a user’s/visitor’s email address, such as when a visitor submits a register or subscribe form, call the **setUserEmail()** function to record the address.

```javascript
// email: user’s email address (string, required)
optimoveSDK.API.setUserEmail(email);
```
Example usage:
```javascript
// general shared function to captures email address from various forms
function updateSDKUserEmail(email){
	// Optimove function
	optimoveSDK.API.setUserEmail(email);
}

// example email variable
var email = 'joe@gmail.com';

// calling the shared function with the relavant variable
updateSDKUserEmail (email);
```
### 7. <a id="record-user-email"></a>Registering the User ID and User Email at the Same Time

In all situations where a single user action requires you to set both the customer ID and email address (e.g., registration, newsletter signup) simultaneously, you should use the **registerUser()** function (instead of calling both [setUserId()](https://github.com/optimove-tech/Web-SDK-Integration-Guide#link-visit-customer) and [setUserEmail()](https://github.com/optimove-tech/Web-SDK-Integration-Guide#record-email)) to ensure the proper registration of the user in Optimove.

```javascript
// SDK_ID: the unique/primary customer ID used by your website to identify registered customers/users. 
// email: user’s email address (string, required)
// eventName: name of the event during which the SDK_ID and email address were captured (string, optional)
// parameters: an array of details for the specified event name (integer/string ,optional)
optimoveSDK.API.registerUser(SDK_ID, email, eventName, parameters);
```
>**Note:** 
> - Event names will be pre-registered in Optimove by your CSM. 
> - You can include optional custom eventName and parameters values in this function call which equivalent to calling the [reportEvent()](https://github.com/optimove-tech/Web-SDK-Integration-Guide#custom-events) function.

**Example usage 1:** SDK_ID and email address without events:
```javascript
// example variables
var SDK_ID = 'JohnDoe';
var email = 'johndoe@gmail.com';

// passing the variables to the SDK function
optimoveSDK.API.registerUser(SDK_ID, email)
```

**Example usage 2:** SDK_ID and email address with custom events:
```javascript
// example variables
var SDK_ID = 'JohnDoe';
var emailAddress = 'johndoe@gmail.com';
var eventName = 'Registration';
var parameters = {
      Newsletter_Signup : true,
      Landing_Page : 'some/landing/page.html'
}

// passing the variables to the SDK function
optimoveSDK.API.registerUser(SDK_ID , email, eventName, parameters);
```


# <a id="advanced-setup"></a>Advanced Setup

The **Advanced Setup** includes everything in the [Basic Setup](https://github.com/optimove-tech/Web-SDK-Integration-Guide#basic-setup) as well as reporting custom events. 

Following your Basic Setup SDK deployment, Optimove's Product Integration Manager will setup a call to help you create and implement the custom events.

>**Note:** 
> The Basic Setup is a pre-requisite to the Advanced one.

### 1. <a id="custom-events"></a>Reporting Custom Events

Your website reports a predefined event to Optimove by using JavaScript to call **reportEvent()** in this format:
```javascript
optimoveSDK.API.reportEvent(<event_name>, <parameter JS object>);
```

>**Note:**
> To see examples of custom events, please visit [Defining the Set of Custom Tracking Events](https://github.com/optimove-tech/SDK-Custom-Events-for-Your-Vertical) .

Eample usage:
```javascript
// an function for adding a product to a specific wish list
function addToWishList(list_name, pid, pname, price) {
   var params = {};
	   params['list_name'] = list_name;
	   params['pid'] = pid;
	   params['name'] = pname;
	   params['price'] = price;
   optimoveSDK.API.reportEvent ('Add_To_Wishlist', params);
}

// calling the add to wish list function with the relavant data
addToWishList('my wish list 1', 123456, 'product name', 1.99);
```

### 2. <a id="server-side-events"></a>How to Report an Custom Event using server-side programming

At this time, events reported in this way will only be used by the Optimove realtime functionality. 
[Click here](https://github.com/optimoveproductintegration/Reporting-Server-Side-Custom-Events) to see how to report custom events using server side programming.

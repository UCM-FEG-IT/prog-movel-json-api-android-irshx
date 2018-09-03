
# Consuming a JSON REST API in Android


There are two parts to processing JSON data in Android using NodeJS and Express:

1.  Writing an app capable of processing the JSON data.
2.  Writing a backend capable of producing the JSON data to be processed.

Because there are two parts to this, naturally I’ve broken the steps down into two guides.

In the first guide – i.e. this one – l’ll walk you through step #1 and teach you how to create an Android app using Android Studio, which allows you to process JSON data from a REST API.

The counterpart of this will show you how to make a REST API using Java is Covered in System Application and Integration . 
Alright, so let’s get started.

In this guide I’ll teach you how to make an Android app which uses the latest  [Volley HTTP library](https://developer.android.com/training/volley/index.html) and allows you to efficiently make network requests and handle the response.

The app will have a text field where you can enter a GitHub username and a button that will retrieve a list of all public GitHub repositories for the provided username.

The repos will be listed in a text box with the name and last updated date.

## Create Our App

Open up Android Studio and create a new project.

![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/Selection_002.png?resize=786%2C500&ssl=1)

Next, fill out the new project form by entering the following:

Application Name:  **AndroidJsonParser**

Company Domain:  **(Your company domain, or if you don’t have one, use example.com)**.

Then, click  **Next**.

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_001.png?resize=807%2C768&ssl=1)

In the _Target Android Devices_  screen, leave everything as default and click **Next**.

![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_002.png?resize=814%2C776&ssl=1)

On the _Add an Activity to Mobile_  screen, choose **Empty Activity**  and then click **Next**.

![](https://i0.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_003.png?resize=804%2C773&ssl=1)

On the _Customize the Activity_  screen, leave everything as default and click **Finish**.

![](https://i0.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_004.png?resize=806%2C771&ssl=1)
Wait a minute or two for the project to load…

## Enable Volley and Internet Access

Let’s add the Volley library to our project.

In the  **Project Explorer**, navigate to  **Gradle Scripts**  >  **build.gradle**  and add the following to the dependencies:

    compile 'com.mcxiaoke.volley:library:1.0.19'

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_005.png?resize=883%2C745&ssl=1)

Save the file.

Then, in the **Project Explorer**  navigate to and open **app** > **manifests** > **AndroidManifest.xml**.

Just above the _application_  tag, add the following line to add the INTERNET permission:

    <uses-permission android:name="android.permission.INTERNET" />


![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_006.png?resize=881%2C746&ssl=1)

## Modify the Layout

Next we are going to want to add the following elements to our activity layout:

-   EditText for a GitHub username
-   Button for getting the data once the username is entered
-   TextView for showing our results

That’s the minimum required for an effective demo.

In the project explorer, navigate to **app**  > **res**  > **layout**  > **activity_main.xml**. Once the file opens, select the **Text**  tab at the bottom of the screen.

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-22_Selection_007.png?resize=1039%2C831&ssl=1)
Now, let’s remove the existing _Hello World!_  text view, and add the following code:

    <EditText
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"    
    android:inputType="textPersonName"    
    android:hint="GitHub Username"   
    android:text="LondonAppDev"   
    android:ems="10"    
    android:layout_alignParentTop="true"    
    android:layout_alignParentLeft="true"   
    android:layout_alignParentStart="true"   
    android:id="@+id/et_github_user"  
    android:layout_alignParentRight="true"  
    android:layout_alignParentEnd="true" /> 
    
    <Button 
    android:text="Get Repos" 
    android:layout_width="wrap_content"  
    android:layout_height="wrap_content"    
    android:layout_below="@+id/et_github_user"  
    android:layout_alignParentLeft="true"    
    android:layout_alignParentStart="true"    
    android:id="@+id/btn_get_repos"    
    android:layout_alignParentRight="true"    
    android:layout_alignParentEnd="true"   
    android:onClick="getReposClicked" />   
    
    <TextView    
    android:layout_width="wrap_content"    
    android:layout_height="wrap_content"    
    android:layout_below="@+id/btn_get_repos"    
    android:layout_alignParentLeft="true"    
    android:layout_alignParentStart="true"    
    android:id="@+id/tv_repo_list"    
    android:layout_alignParentBottom="true"   
    android:layout_alignParentRight="true"   
    android:scrollbars="vertical"   
    android:layout_alignParentEnd="true" />



![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_001.png?resize=1041%2C831&ssl=1)

## Write our Java Code

Now, let’s write the logic for our app.

The first thing we’ll do is import the packages we need to make our app work.

You can actually do this as you go along, using the **Alt** + **Enter**  shortcut to auto-import. However, I found this didn’t work for all of the imports (some of the Volley and JSON packages I needed to import manually).

For simplicity, lets delete any existing import statements, and replace with the following (note: make sure you leave the **package** declaration on the first line, as this will be different for each person).

	import android.support.v7.app.AppCompatActivity; import android.os.Bundle; import android.text.method.ScrollingMovementMethod;
	import android.util.Log; import android.view.View; 
	import android.widget.Button; import android.widget.EditText; 
	import android.widget.TextView; import com.android.volley.RequestQueue;
	import com.android.volley.VolleyError; 
	import com.android.volley.Response; 
	import com.android.volley.Request; 
	import com.android.volley.toolbox.JsonArrayRequest; 
	import com.android.volley.toolbox.Volley; 
	import org.json.JSONArray; 
	import org.json.JSONObject; import org.json.JSONException;



It will look something like this

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_003.png?resize=916%2C882&ssl=1)

The reason some are grey is because Android Studio is letting us know which ones are unused. Don’t worry, we’ll use them later.

Now let’s add some instance variables. Instance variables are defined when we initialise our app. These variables are used in various methods later on in the code.

We define them immediately below the “Public class MainActivity…” and above the first “@Override” statement:

	EditText etGitHubUser; // This will be a reference to our GitHub username input. 
	Button btnGetRepos; // This is a reference to the "Get Repos" button. TextView tvRepoList; // This will reference our repo list text box.
	RequestQueue requestQueue; // This is our requests queue to process our HTTP requests. 
	String baseUrl = "https://api.github.com/users/"; // This is the API base URL (GitHub API) 
	String url; // This will hold the full URL which will include the username entered in the etGitHubUser.


So our code now looks like this:

![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_004.png?resize=921%2C891&ssl=1)

Getting there…

Next, let’s update our “onCreate” to register our views and setup our queue. As the name suggests, “onCreate()” is the function that is created when our Android Activity (the screen in-front of our code) is created.

We need to link the view elements we created earlier (the text input, button and text output) with the instance variables we defined above, so we can use them in our code.

Update the onCreate method to look like this:

	@Override
	protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState); // This is some magic for Android to load a previously saved state for when you are switching between actvities.
	setContentView(R.layout.activity_main); // This links our code to our layout which we defined earlier.
	
	this.etGitHubUser = (EditText) findViewById(R.id.et_github_user); // Link our github user text box.
	this.btnGetRepos = (Button) findViewById(R.id.btn_get_repos); // Link our clicky button.
	this.tvRepoList = (TextView) findViewById(R.id.tv_repo_list); // Link our repository list text output box.
	this.tvRepoList.setMovementMethod(new ScrollingMovementMethod()); // This makes our text box scrollable, for those big GitHub contributors with lots of repos :)
	
	requestQueue = Volley.newRequestQueue(this); // This setups up a new request queue which we will need to make HTTP requests.
	}



So now our code looks like this:

![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_005.png?resize=954%2C881&ssl=1)

Next, let’s add some helper functions to our code.

I described what each function does in the comments block.

Insert the following below the onCreate() block which we just updated (below the end bracket):

	private void clearRepoList() {
		// This will clear the repo list (set it as a blank string).
		this.tvRepoList.setText("");
	}

	private void addToRepoList(String repoName, String lastUpdated) {
		// This will add a new repo to our list.
		// It combines the repoName and lastUpdated strings together.
		// And then adds them followed by a new line (\n\n make two new lines).
		String strRow = repoName + " / " + lastUpdated;
		String currentText = tvRepoList.getText().toString();
		this.tvRepoList.setText(currentText + "\n\n" + strRow);

	}

	private void setRepoListText(String str) {
		// This is used for setting the text of our repo list box to a specific string.
		// We will use this to write a "No repos found" message if the user doens't have any.
		this.tvRepoList.setText(str);
	}



So the code will look something like this:

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_006.png?resize=956%2C885&ssl=1)

Alright, so this is the part where we make our HTTP request.

Insert this code underneath our last function (setRepoListText). This code might look confusing but it’s actually quite simple. I’ve described what each section does in the inline comments. If you want to gain a more in-depth understanding, I highly recommend you read the official guide [Transmitting Network Data Using Volley](https://developer.android.com/training/volley/index.html).

	private void getRepoList(String username) {
	// First, we insert the username into the repo url.
	// The repo url is defined in GitHubs API docs (https://developer.github.com/v3/repos/).
	
	this.url = this.baseUrl + username + "/repos";
	
	// Next, we create a new JsonArrayRequest. This will use Volley to make a HTTP request
	// that expects a JSON Array Response.
	// To fully understand this, I'd recommend readng the office docs: https://developer.android.com/training/volley/index.html
	
	JsonArrayRequest arrReq = new JsonArrayRequest(Request.Method.GET, url,

	new Response.Listener<JSONArray>() {

		@Override

		public void onResponse(JSONArray response) {
		// Check the length of our response (to see if the user has any repos)
		
		if (response.length() > 0) {
		
		// The user does have repos, so let's loop through them all.

		for (int i = 0; i < response.length(); i++) {
	
			try {

				// For each repo, add a new line to our repo list.
				JSONObject jsonObj = response.getJSONObject(i);
				String repoName = jsonObj.get("name").toString();
				String lastUpdated = jsonObj.get("updated_at").toString();
				addToRepoList(repoName, lastUpdated);

			} catch (JSONException e) {

				// If there is an error then output this to the logs.

				Log.e("Volley", "Invalid JSON Object.");
				}
				}

		} else {
				// The user didn't have any repos.
				setRepoListText("No repos found.");
				}
			}
		},

	new Response.ErrorListener() {
		@Override
		public void onErrorResponse(VolleyError error) {
			// If there a HTTP error then add a note to our repo list.
			setRepoListText("Error while calling REST API");
			Log.e("Volley", error.toString());
			}
			}

		);

	// Add the request we just defined to our request queue.
	// The request queue will automatically handle the request as soon as it can.
	requestQueue.add(arrReq);
	}



Now our code looks like this:

![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_007.png?resize=955%2C984&ssl=1)

One final code change to make. You may have noticed above that in our Layout Snippet we added the following on line 25:

android:onClick="getReposClicked"


This basically tells Android that when the user clicks that button, call the **getReposClicked()**  function in our app.

So the last step is to define this function.

Add this code below the Volley function we just added:

	public void getReposClicked(View v) {

		// Clear the repo list (so we have a fresh screen to add to)

		clearRepoList();

		// Call our getRepoList() function that is defined above and pass in the

		// text which has been entered into the etGitHubUser text input field.

		getRepoList(etGitHubUser.getText().toString());

	}



Now the code looks like this:

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_008.png?resize=956%2C808&ssl=1)

Alright, **the code is complete!**

## Test Our App

Finally, let’s test our app.

At the top of Android Studio, click on the play button:

![](https://i2.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_009.png?resize=578%2C146&ssl=1)

In the _Select Deployment Target_  screen, choose either a **Connected Device**  (if you have a real Android Device that you use to test your apps on), or a device under **Available Virtual Devices**.

If you don’t already have a device setup, then you can click on **Create New Virtual Device**  and follow the steps on-screen to create a new one.

![](https://i0.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_010-2.png?resize=633%2C506&ssl=1)
Alright, once that’s done, your app should load on the selected target.

Test it out! Enter your GitHub username and watch it retrieve a list of all your GitHub repos right before your eyes.

[![](https://i1.wp.com/www.londonappdeveloper.com/wp-content/uploads/2017/02/2017-02-23_Selection_011.png?resize=403%2C800&ssl=1)  

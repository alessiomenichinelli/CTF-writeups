
After looking at the website, I realized that I need to find a way to unlock the feature before the countdown finishes. I downloaded the code and began examining the files. There are two different applications: the website hosted on port 1337 and a validation server hosted on port 1338.

![[Pasted image 20240901105715.png]]

Analyzing the website's code, I noticed two main functions: `release()`, which manages access to the feature, and `feature()`, which counts words by executing a shell code. I began exploring ways to unlock the feature.

![[Pasted image 20240901111727.png]]

Access to the feature is checked using the `validate_server` function. This function verifies if `date` is after `NEW_FEATURE_RELEASE`, which is set to 7 days after the current date. 

![[Pasted image 20240901111424.png]]

The `date` value is retrieved by sending a request to a server, which is set by default to `'http://127.0.0.1:1338'`, the port of the validation server app. Upon inspecting the code, I found that the validation server responds with a JSON object containing the current date. Further, while reviewing the `release()` function, I noticed that the default validation server can be changed in debug mode (managed simply with an argument) using cookies.

![[Pasted image 20240901111837.png]]

It is now clear that I need to create a fake validation server to manipulate the `date` value. I wrote a simple Flask app that returns a date after the `NEW_FEATURE_RELEASE`, and by using `ngrok`, I made it publicly accessible.

I created a cookie by adding the `validation_server` value to the default preferences and encoded it in base64 (`{"theme": "light", "language": "en", "validation_server": "fakeserver"}`). I then visited the site in debug mode (`/release?debug=true`) and gained access to the feature.

![[Pasted image 20240901113106.png]]

The feature is a word counter that executes a `wc` command to an `echo` command output and prints the output.

![[Pasted image 20240901113119.png]]

![[Pasted image 20240901235246.png]]

Now it is easy to do a command injection using `$(cat flag.txt) #`.

![[Pasted image 20240901235754.png]]




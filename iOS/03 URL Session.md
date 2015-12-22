[TOC]
#01 URL Session With System-Provided Delegates
###1.1 请求过程
* Create a session configuration. For background sessions, this configuration must contain a unique identifier. Store that identifier, and use it to reassociate with the session if your app crashes or is terminated or suspended.<br>
* Create a session, specifying a configuration object and a nil delegate.<br>
* Create task objects within a session that each represent a resource request.<br>
Each task starts out in a suspended state. After your app calls `resume` on the task, it begins downloading the specified resource.<br>
The task objects are subclasses of `NSURLSessionTask—NSURLSessionDataTask`, `NSURLSessionUploadTask`, or `NSURLSessionDownloadTask`, depending on the behavior you are trying to achieve. These objects are analogous to `NSURLConnection` objects, but give you more control and a unified delegate model.<br>
Although your app can (and typically should) add more than one task to a session, for simplicity, the remaining steps describe the life cycle in terms of a single task.<br>

> Important: If you are using the `NSURLSession` class without providing delegates, your app must create tasks using a call that takes a `completionHandler `parameter, because otherwise it cannot obtain data from the class.<br>

* For a download task, during the transfer from the server, if the user tells your app to pause the download, cancel the task by calling `cancelByProducingResumeData:` method. Later, pass the returned resume data to either the `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:` method to create a new download task that continues the download.<br>
* When a task completes, the `NSURLSession` object calls the task’s completion handler.<br>

> Note: `NSURLSession` does not report server errors through the error parameter. The only errors your app receives through the error parameter are client-side errors, such as being unable to resolve the hostname or connect to the host. The error codes are described in __URL Loading System Error Codes__.<br>
> 
> Server-side errors are reported through the HTTP status code in the `NSHTTPURLResponse` object. For more information, read the documentation for the `NSHTTPURLResponse` and `NSURLResponse` classes.<br>

* When your app no longer needs a session, invalidate it by calling either `invalidateAndCancel` (to cancel outstanding tasks) or `finishTasksAndInvalidate` (to allow outstanding tasks to finish before invalidating the object).<br>

#02 URL Session With Custom Delegates<br>
###2.1 什么情况下使用
* 后台下载、上传
* 需要验证信息：HTTPS
* 通过Body Stream上传文件

###2.2 代理的作用
* When used with download tasks, the `NSURLSession` object uses the delegate to provide your app with a file URL where it can obtain the downloaded data.Delegates are required for all background downloads and uploads. These delegates must provide all of the delegate methods in the `NSURLSessionDownloadDelegate` protocol.
* Delegates can handle certain authentication challenges.
* Delegates provide body streams for uploading stream-based data to the remote server.
* Delegates can decide whether to follow HTTP redirects or not.
* The `NSURLSession` object uses the delegate to provide your app with the status of each transfer. Data task delegates receive both an initial call, in which you can convert the request into a download, and subsequent calls, which provide pieces of data as they arrive from the remote server.
* Delegates are one way in which the `NSURLSession` object can tell your app when a transfer is complete.

###2.3 请求过程
1. 创建session configuration：Create a session configuration. For background sessions, this configuration must contain a unique identifier. Store that identifier, and use it to reassociate with the session if your app crashes or is terminated or suspended.
2. 创建session：Create a session, specifying a configuration object and, optionally, a delegate.

3. 创建task：Create task objects within a session that each represent a resource request.<br>
Each task starts out in a suspended state. After your app calls `resume` on the task, it begins downloading the specified resource.<br>
The task objects are subclasses of `NSURLSessionTask`—`NSURLSessionDataTask`, `NSURLSessionUploadTask`, or `NSURLSessionDownloadTask`, depending on the behavior you are trying to achieve. These objects are analogous to `NSURLConnection` objects, but give you more control and a unified delegate model.<br>
Although your app can (and typically should) add more than one task to a session, for simplicity, the remaining steps describe the life cycle in terms of a single task.<br>
4. 加密验证：If the remote server returns a status code that indicates authentication is required and if that authentication requires a connection-level challenge (such as an SSL client certificate), `NSURLSession` calls an authentication challenge delegate method.
	* For session-level challenges—`NSURLAuthenticationMethodNTLM`, `NSURLAuthenticationMethodNegotiate`, `NSURLAuthenticationMethodClientCertificate`, or `NSURLAuthenticationMethodServerTrust`—the `NSURLSession` object calls the session delegate’s `URLSession:didReceiveChallenge:completionHandler:` method. If your app does not provide a session delegate method, the `NSURLSession` object calls the task delegate’s `URLSession:task:didReceiveChallenge:completionHandler:` method to handle the challenge.
	* For non-session-level challenges (all others), the `NSURLSession` object calls the session delegate’s `URLSession:task:didReceiveChallenge:completionHandler:` method to handle the challenge. If your app provides a session delegate and you need to handle authentication, then you must either handle the authentication at the task level or provide a task-level handler that calls the per-session handler explicitly. The session delegate’s `URLSession:didReceiveChallenge:completionHandler:` method is not called for non-session-level challenges.
	* If authentication fails for an upload task, if the task’s data is provided from a stream, the `NSURLSession` object calls the delegate’s `URLSession:task:needNewBodyStream:` delegate method. The delegate must then provide a new `NSInputStreamobject` to provide the body data for the new request.<br>
For more information about writing an authentication delegate method for `NSURLSession`, read __Authentication Challenges and TLS Chain Validation__.
5. 请求转移：Upon receiving an HTTP redirect response, the `NSURLSession` object calls the delegate’s `URLSession:task:willPerformHTTPRedirection:newRequest:completionHandler:` method. That delegate method calls the provided completion handler with either the provided `NSURLRequest` object (to follow the redirect), a new `NSURLRequest` object (to redirect to a different URL), or nil (to treat the redirect’s response body as a valid response and return it as the result).
	* If the redirect is followed, go back to step 4 (authentication challenge handling).
	* If the delegate does not implement this method, the redirect is followed up to the maximum number of redirects.
6. download task开始：For a (re-)download task created by calling `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:`, `NSURLSession` calls the delegate’s `URLSession:downloadTask:didResumeAtOffset:expectedTotalBytes:` method with the new task object.
7. data task开始：For a data task, the `NSURLSession` object calls the delegate’s `URLSession:dataTask:didReceiveResponse:completionHandler:` method. Decide whether to convert the data task into a download task, and then call the completion callback to continue receiving data or downloading data.<br>
If your app chooses to convert the data task to a download task, NSURLSession calls the delegate’s URLSession:dataTask:didBecomeDownloadTask: method with the new download task as a parameter. After this call, the delegate receives no further callbacks from the data task, and begins receiving callbacks from the download task.
8. 通过stream上传：If the task was created with uploadTaskWithStreamedRequest:, NSURLSession calls the delegate’s URLSession:task:needNewBodyStream: method to provide the body data.
9. 上传进度：During the initial upload of body content to the server (if applicable), the delegate periodically receives `URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:` callbacks that report the progress of the upload.
10. 下载（获取）进度：During the transfer from the server, the task delegate periodically receives a callback to report the progress of the transfer. For a download task, the session calls the delegate’s `URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:` method with the number of bytes successfully written to disk. For a data task, the session calls the delegate’s URLSession:dataTask:didReceiveData:method with the actual pieces of data as they are received.<br>
暂停下载、取消下载：For a download task, during the transfer from the server, if the user tells your app to pause the download, cancel the task by calling the cancelByProducingResumeData: method.<br>
继续下载：if the user asks your app to resume the download, pass the returned resume data to either the `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:` method to create a new download task that continues the download, then go to step 3 (creating and resuming task objects).

11. 缓存：For a data task, the `NSURLSession` object calls the delegate’s `URLSession:dataTask:willCacheResponse:completionHandler:` method. Your app should then decide whether to allow caching. If you do not implement this method, the default behavior is to use the caching policy specified in the session’s configuration object.

12. 下载结束：If a download task completes successfully, then the `NSURLSession` object calls the task’s `URLSession:downloadTask:didFinishDownloadingToURL:` method with the location of a temporary file. Your app must either read the response data from this file or move it to a permanent location in your app’s sandbox container directory before this delegate method returns.

13. 请求结束：When any task completes, the `NSURLSession` object calls the delegate’s `URLSession:task:didCompleteWithError:` method with either an error object or nil (if the task completed successfully).<br>
If the task failed, most apps should retry the request until either the user cancels the download or the server returns an error indicating that the request will never succeed. Your app should not retry immediately, however. Instead, it should use reachability APIs to determine whether the server is reachable, and should make a new request only when it receives a notification that reachability has changed.<br>
If the download task can be resumed, the NSError object’s userInfo dictionary contains a value for the `NSURLSessionDownloadTaskResumeData` key. Your app should pass this value to call `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:` to create a new download task that continues the existing download.<br>
If the task cannot be resumed, your app should create a new download task and restart the transaction from the beginning.<br>
In either case, if the transfer failed for any reason other than a server error, go to step 3 (creating and resuming task objects).<br>
14. If the response is multipart encoded, the session may call the delegate’s `didReceiveResponse` method again, followed by zero or more additional `didReceiveData` calls. If this happens, go to step 7 (handling the `didReceiveResponse` call).
15. 取消请求：When you no longer need a session, invalidate it by calling either `invalidateAndCancel` (to cancel outstanding tasks) or `finishTasksAndInvalidate` (to allow outstanding tasks to finish before invalidating the object).<br>
After invalidating the session, when all outstanding tasks have been canceled or have finished, the session sends the delegate a `URLSession:didBecomeInvalidWithError:` message. When that delegate method returns, the session disposes of its strong reference to the delegate.<br>

>Important: The session object keeps a strong reference to the delegate until your app explicitly invalidates the session. If you do not invalidate the session, your app leaks memory.

If your app cancels an in-progress download, the `NSURLSession` object calls the delegate’s `URLSession:task:didCompleteWithError:` method as though an error occurred.
###2.4 data task
If you are using a custom delegate to retrieve data, the delegate must implement at least the following methods:

* `URLSession:dataTask:didReceiveData:` provides the data from a request to your task, one piece at a time.

* `URLSession:task:didCompleteWithError:` indicates to your task that the data has been fully received.<br>

If your app needs to use the data after its `URLSession:dataTask:didReceiveData:` method returns, your code is responsible for storing the data in some way.<br>
For example, a web browser might need to render the data as it arrives along with any data it has previously received. To do this, it might use a dictionary that maps the task object to an `NSMutableData` object for storing the results, and then use the `appendData:` method on that object to append the newly received data.
###2.5 downloading file

At a high level, downloading a file is similar to retrieving data. Your app should implement the following delegate methods:

* `URLSession:downloadTask:didFinishDownloadingToURL:` provides your app with the URL to a temporary file where the downloaded content is stored.


> Important: Before this method returns, it must either open the file for reading or move it to a permanent location. When this method returns, the temporary file is deleted if it still exists at its original location.

* `URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:` provides your app with status information about the progress of the download.

* `URLSession:downloadTask:didResumeAtOffset:expectedTotalBytes:` tells your app that its attempt to resume a previously failed download was successful.

* `URLSession:task:didCompleteWithError:` tells your app that the download failed.<br>

If you schedule the download in a background session, the download continues when your app is not running. If you schedule the download in a standard or ephemeral session, the download must begin anew when your app is relaunched.<br>
During the transfer from the server, if the user tells your app to pause the download, your app can cancel the task by calling the `cancelByProducingResumeData:`method. Later, your app can pass the returned resume data to either the `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:method` to create a new download task that continues the download.<br>
If the transfer fails, your delegate’s `URLSession:task:didCompleteWithError:` method is called with an NSError object. If the task is resumable, that object’s userInfo dictionary contains a value for the `NSURLSessionDownloadTaskResumeData` key; your app can pass the returned resume data to either the `downloadTaskWithResumeData:` or `downloadTaskWithResumeData:completionHandler:` method to create a new download task that retries the download.<br>
###2.6 upload body
* Use an NSData object if your app already has the data in memory and has no reason to dispose of it.
* Use a file if the content you are uploading exists as a file on disk, if you are doing background transfer, or if it is to your app’s benefit to write it to disk so that it can release the memory associated with that data.
* Use a stream if you are receiving the data over a network or are converting existing `NSURLConnection` code that provides the request body as a stream.

Regardless of which style you choose, if your app provides a custom session delegate, that delegate should implement the `URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend:` delegate method to obtain upload progress information.<br>
Additionally, if your app provides the request body using a stream, it must provide a custom session delegate that implements the `URLSession:task:needNewBodyStream:` method, described in more detail in Uploading Body Content Using a Stream.<br>
####2.6.1 Uploading Body Content Using a File
To upload body content from a file, your app calls either the `uploadTaskWithRequest:fromFile:` or `uploadTaskWithRequest:fromFile:completionHandler:` method to create an upload task, and provides a file URL from which the task reads the body content.<br>
The session object computes the _Content-Length_ header based on the size of the data object. If your app does not provide a value for the _Content-Type_ header, the session also provides one.<br>
Your app can provide any additional header information that the server might require as part of the URL request object.<br>
####2.6.2 Uploading Body Content Using a Stream
To upload body content using a stream, your app calls the `uploadTaskWithStreamedRequest:` method to create an upload task. Your app provides a request object with an associated stream from which the task reads the body content.<br>
Your app must provide any additional header information that the server might _require—content_ type and length, for example—as part of the URL request object.<br>
In addition, because the session cannot necessarily rewind the provided stream to re-read data, your app is responsible for providing a new stream in the event that the session must retry a request (for example, if authentication fails). To do this, your app provides a `URLSession:task:needNewBodyStream:` method. When that method is called, your app should perform whatever actions are needed to obtain or create a new body stream, and then call the provided completion handler block with the new stream.<br>

> Note: Because your app must provide a `URLSession:task:needNewBodyStream:` delegate method if it provides the body through a stream, this technique is incompatible with using a system-provided delegate.<br>

####2.6.3 Uploading a File Using a Download Task
To upload body content for a download task, your app must provide either an `NSData` object or a body stream as part of the `NSURLRequest` object provided when it creates the download request.<br>
If you provide the data using a stream, your app must provide a `URLSession:task:needNewBodyStream:` delegate method to provide a new body stream in the event of an authentication failure. This method is described further in Uploading Body Content Using a Stream.<br>
The download task behaves just like a data task except for the way in which the data is returned to your app.<br>
#03 Authentication Challenges and TLS Chain Validation
* `- URLSession:didReceiveChallenge:completionHandler:`
* `- URLSession:task:didReceiveChallenge:completionHandler:`
* `- connection:didReceiveAuthenticationChallenge:`

###3.1 Responding to an Authentication Challenge
The following are the three ways you can respond to the `connection:didReceiveAuthenticationChallenge:` delegate method.<br>
###3.2 Providing Credentials
To attempt to authenticate, the application should create an `NSURLCredential` object with authentication information of the form expected by the server. You can determine the server’s authentication method by calling authenticationMethod on the protection space of the provided authentication challenge. Some authentication methods supported by `NSURLCredential` are:<br>

* HTTP basic authentication (`NSURLAuthenticationMethodHTTPBasic`) requires a user name and password. Prompt the user for the necessary information and create an NSURLCredential object with c`redentialWithUser:password:persistence:`.<br>
* HTTP digest authentication (`NSURLAuthenticationMethodHTTPDigest`), like basic authentication, requires a user name and password. (The digest is generated automatically.) Prompt the user for the necessary information and create an NSURLCredentialobject with `credentialWithUser:password:persistence:`.<br>
* Client certificate authentication (`NSURLAuthenticationMethodClientCertificate`) requires the system identity and all certificates needed to authenticate with the server. Create an `NSURLCredential` object with `credentialWithIdentity:certificates:persistence:`.<br>
* Server trust authentication (`NSURLAuthenticationMethodServerTrust`) requires a trust provided by the protection space of the authentication challenge. Create an `NSURLCredential` object with `credentialForTrust:`.<br>

After you’ve created the `NSURLCredential` object:<br>

* For `NSURLSession`, pass the object to the authentication challenge’s sender using the provided completion handler block.<br>
* For `NSURLConnection` and `NSURLDownload`, pass the object to the authentication challenge’s sender with `useCredential:forAuthenticationChallenge:`.<br>

###3.3 Continuing Without Credentials
If the delegate chooses not to provide a credential for the authentication challenge, it can attempt to continue without one.<br>

* For `NSURLSession`, pass one of the following values to the provided completion handler block:`NSURLSessionAuthChallengePerformDefaultHandling` processes the request as though the delegate did not provide a delegate method to handle the challenge.<br>
`NSURLSessionAuthChallengeRejectProtectionSpace` rejects the challenge. Depending on the authentication types allowed by the server’s response, the URL loading class may call this delegate method more than once, for additional protection spaces.

* For `NSURLConnection` and `NSURLDownload`, call `continueWithoutCredentialsForAuthenticationChallenge:` on `[challenge sender]`.<br>
Depending on the protocol implementation, continuing without credentials may either cause the connection to fail, resulting in a `connectionDidFailWithError:` message, or return alternate URL contents that don’t require authentication.

###3.4 Canceling the Connection<br>
The delegate may also choose to cancel the authentication challenge.<br>

* For `NSURLSession`, pass `NSURLSessionAuthChallengeCancelAuthenticationChallenge` to the provided completion handler block.<br>
* For `NSURLConnection` or `NSURLDownload`, call `cancelAuthenticationChallenge:` on [challenge sender]. The delegate receives a `connection:didCancelAuthenticationChallenge:` message, providing the opportunity to give the user feedback.<br>


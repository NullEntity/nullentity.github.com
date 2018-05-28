---
layout: post
title:  "Identity Server and Auth0"
date:	Sun May 27 22:11:16 EDT 2018
categories: asp.net
---
For some background, I'm an operator on the [C# Discord server][http://aka.ms/csharp-discord]. I like to hang out there to socialize and help people with C#. 

Today a user named AndersNicolai had a very strange error when trying to use Identity Server 4 with ASP.NET Core 2. He was trying to configure Auth0 as an external OpenID Connect provider. The following image is the error he saw:
![Indentity Server 4 error]({{ "/assets/indentity-server-error.png" | absolute_url }})
The error reads:
An unhandled exception occured while processing the Request.
InvalidOperationException: Cannot redirect to the end session endpoint, the configuration may be missing or invalid.

New users to C# will commonly ask for help before putting in any bit of effort. So my first assumption was that the configuration was missing or invalid. I asked the user to post his ServiceConfiguration method for the server and client. Hm, the configurations seemed to look correct. So next I busted out the Google.

It took me a bit of Googling to find the solution to this. [One of the possible solutions](https://stackoverflow.com/a/49815962/1217412) I found involved injecting an event. That didn't seem right to me though. I wanted to know _why_ the server couldn't redirect to the logout endpoint.

[An Auth0 forum post](https://community.auth0.com/t/asp-net-core-migrate-from-1-1-to-2-0/7997) from the Google search results sounded interesting. Anders's config showed that he was trying to use Auth0 as his external provider! The answer to the forum post states that Identity Server tries to automatically discover the endpoint for OpenID Connect providers. Auth0 doesn't provide the endpoint automatically because their implementation requires extra parameters. And that's where the the Stack Overflow answer from before fits in! You need to add a handler for OnRedirectToIdentityProviderForSignOut to manually specify the SignOut url.

You can view the Auth0 sample implementation [here](https://github.com/auth0-samples/auth0-aspnetcore-mvc-samples/blob/master/Quickstart/01-Login/SampleMvcApp/Startup.cs#L63).

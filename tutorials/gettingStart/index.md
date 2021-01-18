# Getting started

**Topics covered on this page**

[What is Omise?](#WhatIs)

- [Setting up your account](#SetUp)
- [Running the examples](#RunExample)
- [Choosing an integration](#ChoosingIntegration)
- [Going live](#GoingLive)

[Have question?](#Question)


Welcome to the Omise Docs! In these pages, you should find everything you need to know to get paid using the Omise API.

This is developer-oriented documentation. If you are not a developer, you can check our comprehensive support articles for non-technical explanations of various concepts or go straight to the plugin overview.

The docs are organized into four sections:
- Guides: Get an introduction to how our system works including a security overview and a guide to collecting card information safely.

- Plugins: Have an existing online store? You may be able to easily integrate our API using one of our plugins.

- Payment Methods: In addition to credit cards, we support an ever-growing list of payment methods. Learn how to implement them through comprehensive walkthroughs complete with code and API response examples.

- API References: Read our full API documentation which includes guidance on authenticating with our API, common error codes, and detailed information on all available endpoints.

<a name="WhatIs" id="whatis"></a>
# What is Omise?

Omise is a PCI-certified payment gateway with an easy-to-use management dashboard and an intuitive REST API allowing integration across a variety of languages and frameworks. We give you the power to implement payments the way you want without the risk and overhead of directly handling payment details.

<a name="SetUp" id="setup"></a>
### Setting up your account

To get started, register for a test account to start working on your implementation before going live.

In both test and live mode, the dashboard provides transaction information, account-level settings, and example code. It also provides your account keys which are needed to perform any action using our API such as creating or refunding a charge. You can find your public and secret account keys by checking Keys under the API heading in the menu.

<a name="RunExample" id="runexample"></a>
# Running the examples
To run any of the sample code on this site, you should replace the sample key shown (skey..., $OMISE_SECRET_KEY, pkey... , or $OMISE_PUBLIC_KEY) with the ones specific to your account found in your dashboard.

<a name="ChoosingIntegration" id="choosingintegration"></a>
# Choosing an integration
Omise integrates with a variety of e-commerce solutions via plugins. For more custom solutions, we have libraries for JavaScript, iOS, Android, and many other languages.

Before using any of the plugins and libraries, please read the documentation specific to that integration.

Whatever the solution, never send sensitive information (for example, credit card details) directly through your server. Check our Security Best Practices to learn more.

<a name="GoingLive" id="goinglive"></a>
# Going live
When you’re ready to go live, you can start the registration process directly via the dashboard.

<a name="Question" id="question"></a>
# Have question
If you are having issues using our service, or would like to learn more, you can check the support forum or email support directly. You can check the current availability of API at status.omise.co or follow @omisestatus on Twitter.
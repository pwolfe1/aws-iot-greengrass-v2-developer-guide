# Understanding results and logs<a name="results-logs"></a>

This section describes how to view and interpret IDT result reports and logs\. 

## Viewing results<a name="view-results"></a>

While running, IDT writes errors to the console, log files, and test reports\. After IDT completes the qualification test suite, it generates two test reports\. These reports are located in `<device-tester-extract-location>/results/<execution-id>/`\. Both reports capture the results from running the qualification test suite\.

The `awsiotdevicetester_report.xml` is the qualification test report that you submit to AWS to list your device in the AWS Partner Device Catalog\. The report contains the following elements:
+ The IDT version\.
+ The AWS IoT Greengrass version that was tested\.
+ The SKU and the device pool name specified in the `device.json` file\.
+ The features of the device pool specified in the `device.json` file\.
+ The aggregate summary of test results\.
+ A breakdown of test results by libraries that were tested based on the device features, such as local resource access, shadow, and MQTT\.

The `GGV2Q_Result.xml` report is in [JUnit XML format](https://llg.cubic.org/docs/junit/)\. You can integrate it into continuous integration and deployment platforms like [Jenkins](https://jenkins.io/), [Bamboo](https://www.atlassian.com/software/bamboo), and so on\. The report contains the following elements:
+ Aggregate summary of test results\.
+ Breakdown of test results by the AWS IoT Greengrass functionality that was tested\.

## Interpreting AWS IoT Device Tester results<a name="interpreting-results-gg"></a>

The report section in `awsiotdevicetester_report.xml` or `awsiotdevicetester_report.xml` lists the tests that were run and the results\.

The first XML tag `<testsuites>` contains the summary of the test run\. For example:

```
<testsuites name="GGQ results" time="2299" tests="28" failures="0" errors="0" disabled="0">
```Attributes used in the `<testsuites>` tag

`name`  
The name of the test suite\.

`time`  
The time, in seconds, that it took to run the qualification suite\.

`tests`  
The number of tests that were run\.

`failures`  
The number of tests that were run, but did not pass\.

`errors`  
The number of tests that IDT couldn't run\.

`disabled`  
Ignore this attribute\. It is not used\.

The `awsiotdevicetester_report.xml` file contains an `<awsproduct>` tag that contains information about the product being tested and the product features that were validated after running a suite of tests\.Attributes used in the `<awsproduct>` tag

`name`  
The name of the product being tested\.

`version`  
The version of the product being tested\.

`features`  
The features validated\. Features marked as `required` are required to submit your board for qualification\. The following snippet shows how this information appears in the `awsiotdevicetester_report.xml` file\.  

```
<name="aws-iot-greengrass-v2-core" value="supported" type="required"></feature>
```

If there are no test failures or errors for the required features, your device meets the technical requirements to run AWS IoT Greengrass and can interoperate with AWS IoT services\. If you want to list your device in the AWS Partner Device Catalog, you can use this report as qualification evidence\.

In the event of test failures or errors, you can identify the test that failed by reviewing the `<testsuites>` XML tags\. The `<testsuite>` XML tags inside the `<testsuites>` tag show the test result summary for a test group\. For example:

```
<testsuite name="combination" package="" tests="1" failures="0" time="161" disabled="0" errors="0" skipped="0">
```

The format is similar to the `<testsuites>` tag, but with a `skipped` attribute that is not used and can be ignored\. Inside each `<testsuite>` XML tag, there are `<testcase>` tags for each test that was run for a test group\. For example:

```
<testcase classname="Security Combination (IPD + DCM) Test Context" name="Security Combination IP Change Tests sec4_test_1: Should rotate server cert when IPD disabled and following changes are made:Add CIS conn info and Add another CIS conn info" attempts="1"></testcase>>
```Attributes used in the `<testcase>` tag

`name`  
The name of the test\.

`attempts`  
The number of times IDT ran the test case\.

When a test fails or an error occurs, `<failure>` or `<error>` tags are added to the `<testcase>` tag with information for troubleshooting\. For example:

```
<testcase classname="mcu.Full_MQTT" name="AFQP_MQTT_Connect_HappyCase" attempts="1">
	<failure type="Failure">Reason for the test failure</failure>
	<error>Reason for the test execution error</error>
</testcase>
```

## Viewing logs<a name="view-logs-gg"></a>

IDT generates logs from test runs in `<devicetester-extract-location>/results/<execution-id>/logs`\. Two sets of logs are generated:

`test_manager.log`  
Logs generated from the Test Manager component of AWS IoT Device Tester \(for example, logs related to configuration, test sequencing, and report generation\)\.

`<test-case-id>.log (for example, lambdaDeploymentTest.log)`  
Logs of the test case within the test group, including logs from the device under test\. Starting with IDT v4\.2\.0, IDT groups the test logs for each test case in a separate *<test\-case\-id>* folder within the `<devicetester-extract-location>/results/<execution-id>/logs/<test-group-id>/` directory\.
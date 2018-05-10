# Generic

These tests provide generic Zimbra supported protocol performance testing.

All these tests depend on the Zimbra JMeter Library zjmeter.jar and assume no SSL usage. See [README.md](../../src/README.md) for details on generating the jar.

The following protocols currently have a basic level of support:

* smtp
  
  [Simple Mail Transfer Protocol](https://tools.ietf.org/html/rfc5321) view [smtp](smtp/smtp.md) for a list of currently supported commands in the jmx.
  
* lmtp
  
  [Local Mail Transfer Protocol](https://tools.ietf.org/html/rfc2033) view [lmtp](lmtp/lmtp.md) for a list of currently supported commands in the jmx.
  
* imap
  
  [Internet Message Access Protocol](https://tools.ietf.org/html/rfc3501) view [imap](imap/imap.md) for a list of currently supported commands in the jmx.
  
* pop
  
  [Post Office Protocol](https://tools.ietf.org/html/rfc5321) view [pop](pop/pop.md) for a list of currently supported commands in the jmx.
  
* zsoap
  
  [Zimbra SOAP API](https://wiki.zimbra.com/wiki/SOAP_API_Reference_Material_Beginning_with_ZCS_8) view [zsoap](zsoap/zsoap.md) for list of currently supported commands in the jmx.

This directory also contains a test mix this jmx combines all the above tests into one jmx file that can be used for very low combined user count tests.

# Properties

## Environment

For each of the supported generic tests they depend on a protocol

|Test |Protocol|
|-----|--------|
|imap |IMAP    |
|lmtp |LMTP    |
|pop  |POP     |
|smtp |SMTP    |
|zsoap|SOAP    |

the environment file defines how to access that protocol and other environment specific information.

|Key                    |Value          |Description                         |
|-----------------------|---------------|------------------------------------|
|&lt;PROTOCOL&gt;.server|host.domain.com|server name for protocol access     |
|&lt;PROTOCOL&gt;.domain|domain.com     |domain name used in e-mail addresses|
|&lt;PROTOCOL&gt;.port  |443            |port to use with web server         |
|ACCOUNTS.csv           |users.csv      |csv file of test accounts (user,password)|
|REQUEST.log            |requests.log             |file to log requests to   |
|user.classpath         |src/build/jar/zjmeter.jar|Zimbra JMeter Java Library|

the SOAP protocol also has these additional properties

|Key                |Value    |Description            |
|-------------------|---------|-----------------------|
|SOAP.admin.user    |admin    |admin account user name|
|SOAP.admin.password|adminpass|admin account password |


## Load

|Key                               |Value|Description                                 |
|----------------------------------|-----|--------------------------------------------|
|LOAD.&lt;PROTOCOL&gt;.users       |1    |concurrent users/threads to run during tests|
|LOAD.&lt;PROTOCOL&gt;.userduration|1    |user login duration in seconds              |
|LOAD.&lt;PROTOCOL&gt;.rampup      |0    |how long to spend ramping up threads        |
|LOAD.&lt;PROTOCOL&gt;.loopcount   |1    |how many times the user/thread repeats      |

## Profile

For any of the generic tests you can define a sequence of commands to execute using this basic format:


|Key                                  |Value|Description                             |
|-------------------------------------|-----|----------------------------------------|
|PROFILE.&lt;PROTOCOL&gt;.type                  |sequence|Specifies type of profile  |
|PROFILE.&lt;PROTOCOL&gt;.&lt;type&gt;.&lt;#&gt;|command |Protocol command to execute|

The command can be of the form: name(arg1=value,arg2=value,...)

See each tests &lt;test&gt;.md file for complete list of currently supported commands and look at the files in the tests profile directory for example profiles.

# Example

Any of these tests can be used following this basic outline of steps:

1. grab a copy of the tests

   ```
   $ get clone https://github.com/Zimbra/zm-load-testing.git 
   $ cd zm-load-testing
   ```

2. create a user.csv file of accounts that can be used for testing zimbra
   generic tests use a csv file of the form: &lt;user&gt;,&lt;password&gt;
   add as many users as you want to test with

   ``` 
   $ vi /tmp/users.csv
   user1,userpass
   ...
   ```

3. create zimbra environment specific property file

   ```
   $ cp tests/generic/zsoap/env.prop /tmp/myenv.prop
   $ vi /tmp/myenv.prop
   modify file appropriately for the zimbra environment you plan to test
   update the users.csv file to the csv file created above
   ```

4. run the test
   note: some property files use relative paths that assume jmeter is run from the repo's top directory

   ```
   $ jmeter -n -q /tmp/myenv.prop -q tests/generic/zsoap/load.prop -q tests/generic/zsoap/profile/basic.prop -t tests/generic/zsoap/zsoap.jmx
   ```

The default load.prop file for all the tests is a single user that runs the specified profile once then exits. Just as you created a custom env.prop you can copy the load.prop file to adjust load as desired.

The same also works for creating custom profiles for these generic tests. The generic tests ideally would support all the protocol defined commands however at this time the jmx files only support a subset of any particular protocols commands. The profile allows you to define any sequence of the available commands to execute.
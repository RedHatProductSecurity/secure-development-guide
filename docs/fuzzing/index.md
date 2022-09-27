# Fuzzing

## Introduction
In the recent times, fuzzing needs no introduction. But essentially it is the process of throwing random (but sometimes organized) data to an application and observing how the particular application behaves. This is specially useful from the security perspectives, because sometimes applications crash or behave in an unexpected way, when fed with invalid data. This kind of behaviour can be exploited by attackers to gain access to sensitive data, or even run arbitrary code.\
\
Consider one of the most simplest examples, where random data is sent to a particular port to test a service.\
\
<code>
while [1]; do cat /dev/urandom | nc -vv target port; done
</code>
\
\
The service is then observed to the moment it crashes, we know that there is some random data combination somewhere which can crash the service. Though reproducing this kind of crashes may be very difficult, unless we have some packet capture mechanism which can exactly record the incoming data and the various states reached by the service because of that.\
\
In the sections below, we will have a brief look at the science behind fuzzing and how it can help secure open source development process. Fuzzing has reached a high maturity level, and this single page of documentation cannot do justice to the various nuances of the process, but intends to present a very high level view of what the process is and how it can be helpful.

## Fuzzers
Specialized applications or libraries can be used for fuzzing depending on the application and what input it requires. Lets try to look at some of the broad catergories:

### File based fuzzers
Applications which process files in some way or the other like image manipulation libraries, data parsers etc, can be tested by using these fuzzers. The fuzzer essentially mangles the files in a the fuzzing corpus in some way and iteratively passes them to the application to observe if the application crashes. If there is a crash the fuzzer can either stop or can continue to produce/record any further crashes. Researchers normally leave these fuzzers running and may interfere only if the fuzzing stops due to some reason. The output of the fuzzing process is then analyzed on a regular basis to check if any flaws/bugs have been found.

### Protocol based fuzzers
These are usually written for a specific network protocol and therefore are really specialized. They usually work by sending mangles protocol packets over the wire or the loopback interface to a designated service and then observing how the service reacts. One such free TLS protocol fuzzer with [tlsfuzzer.](https://github.com/tlsfuzzer/tlsfuzzer)  While it uses fuzzing techniques for testing (randomisation of passed in inputs), the scripts are generally written in a way that verifies correct error handling: unlike typical fuzzers it doesn't check only that the system under test didn't crash, it checks that it returned correct error messages.

### Web application fuzzers
Since web appliation are specialized applications, they need special fuzzers. You may perhaps add [browser fuzzers](https://security.googleblog.com/2019/07/chrome-fuzzer-program-update-and-how-to.html)  in this catergory also. They can be of various types, depending on what you want to find. For example some of them are [specialized web app security testers](https://portswigger.net/burp/communitydownload), while others can use to used to find [SQL injection issues in your web application](https://sqlmap.org/). The options available here are just too many to list in this document.

### Instrumented fuzzers
Not strictly a different catergory, but instrumented fuzzers basically insert small hooks into the code during the compile process, which ensures that all parts of the code get a good coverage. Therefore you need the source code to be available and needs to be compiled with the fuzzer libraries. Recently these kind of fuzzers have been quite succesful at finding huge number of previously uncovered flaws in various applications and libraries.\
\
One very good example of Instrumented fuzzers is [American fuzzy lop](https://lcamtuf.coredump.cx/afl/). Compared to other instrumented fuzzers, afl-fuzz is designed to be practical: it has modest performance overhead, uses a variety of highly effective fuzzing strategies and effort minimization tricks, requires essentially no configuration, and seamlessly handles complex, real-world use cases - say, common image parsing or file compression libraries.\
\
Diagram below shows AFL in action (Image taken from its website at: https://lcamtuf.coredump.cx/afl/)\
![AFL in action](../images/afl_screen.png)

## Understanding resource requirements for fuzzing
Fuzzing is a resource intensive process, which needs time, computing resources etc. In the end both translate to more money. So its really a corporate decision to figure out how much money should be spent to uncover any flaws. (If any flaws are found at the end of the process) or are we better off using other techniques described in this guide like SAST for example or code-reviews. It is always useful to fuzz any API end points or user interfaces like login screen etc, which are open on the internet. As compared to that applications on the intranet may not be given so much importance given the fact that they are exposed to a limited audience internally.

## When is fuzzing useful?
As described above, applications on the internet or on an untrusted networks are easy targets for attackers and fuzzing could help discover flaws in them. Typically applications which need lot of user steps may not be good fuzzing targets for example consider an internal company application which needs a login after which you have to get a kerberos ticket and then can be used to upload expense reports, such applications may be difficult to fuzz or may require more specialized techniques. 

## Some open source fuzzing initiatives
- oss fuzz
- clusterfuzz

## Closing remarks
Fuzzing in an excellent method of discovering flaws. But its resource intensive. Though a right combination of identication of high value targets and fuzzing methods can help uncover and fix lot of security flaws and help organizations save money in the long term.

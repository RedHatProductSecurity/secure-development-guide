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

# Fuzzing as black box/ gray box testing

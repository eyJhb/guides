# IPMI
## Invalid jar - not signed
Locate `/etc/java-8-openjdk/security/java.security`, find the lines with `disabledalgorithms`, and comment them (there might be more than one).

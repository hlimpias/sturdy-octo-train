This guide assumes you have a moderate understanding of how to use a command-line environment, and how to forward ports on your router if you wish to make your server available over the Internet (which you almost certainly do, unless you're all on the same network in which case why don't you just use a physical set of Cards Against Humanity?).

Prerequisites:
* Java 7 JDK: http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html
* Maven: http://maven.apache.org/download.cgi
 * Windows setup tips: http://maven.apache.org/guides/getting-started/windows-prerequisites.html
* Source code: https://github.com/ajanata/PretendYoureXyzzy/archive/master.zip

Unzip the source code into some directory. Open a command prompt in that directory. Copy build.properties.example to build.properties; the defaults work for a quick-and-dirty server. Run `mvn clean package war:exploded jetty:run`. Open http://localhost:8080/game.jsp. You're done.

I'm not presently able to help with situation-specific setup.
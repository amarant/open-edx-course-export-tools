# Open edX course export tools

## edx-course-export-flatten
A course export from the edx platform consists of an archive with many xml and xml-like files scattered over several folders.
- edx-course-export-flatten.sh
  - script to run the xsl transformation using the xsl stylesheet on the course export folder
- edx-course-export-flatten.xsl
  - the xsl stylesheet with the transformation rules combining all the xml-files into one xml document

### To flatten the course export into a single xml file
1. First, we need to unpack the edx course export archive and give it a sensible name. Here I am assuming that you already have the the archive in the current folder. In this case the extracted archive contained the folder called 'course'.

   ```
   tar -xvzf archive-name.tar.gz
   mv course some-folder-name
   ```

2. process the course export files (in the **some-folder-name** folder), generating a flat xml and redirecting to file.

   ```
   edx-course-export-flatten.sh some-folder-name > some-course-name.xml
   ```

## Requirements
Detailed install-instructions follow further down this page.
You need:
- a terminal client to run commands
- java runtime environment (required by saxon)
- the saxon xsl processor (supporing xsl version 3) needs to be in the PATH accessible from the command "saxon". Instructions on installing the saxon processor is found further down on the page.
- a symlink pointing to edx-course-flatten.sh needs to be in a folder that is in the PATH
- the references to the edx-course-export-flatten.xsl stylesheet in edx-course-export-flatten.sh needs to be updated to match the full path on your system

## Installing open edX course export tools
Note 1: All instructions here assume that you have root access to your system and will install to /usr/local/bin. If you wish to install in a different location, for instance /home/username/bin, you will have to modify these instructions accordingly, as well as updating edx-course-export-flatten.sh with the corrected absolute/rooted path of the xsl stylesheet.
Also the folder /home/username/bin needs to be set up in the system's search path so you can run commands from that folder. If in doubt, see this article on [how to add a directory to system path on linux]( https://www.computerhope.com/issues/ch001647.htm)

Note 2: these instructions will assume a debian type linux os. Users with other linux/unix flavours can adapt accordingly.

```
cd /usr/local/bin
sudo git clone https://github.com/eirikhanssen/open-edx-course-export-tools.git
sudo ln -s open-edx-course-export-tools/edx-course-export-flatten.sh
```

## Installing requirements
You need the ability to run a xslt processor capable of running xslt version 3 stylesheets, I recommend the saxon processor for this, but possibly you could use another processor. The saxon product have several version. For the stylesheets in this package, the free HE (Home Edition) version of the Saxon processor will be sufficient.

- **saxon xslt/xquery processor** - needed to perform the xslt transformations
- **java runtime environment** - needed to run the saxon processor

### Checking if java is installed
```
java -version
```
This should output something along the lines of:
```
openjdk version "1.8.0_151"
OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-0ubuntu0.16.04.2-b12)
OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)
```
Obviously you might have a different version than this, but as long as it is fairly recent, it should not matter much. From the official saxon documentation in [Saxon Prerequisites](http://www.saxonica.com/documentation/#!about/installationjava/prerequisites): "Saxon 9.8 requires at least Java SE 6 (also known as JDK 1.6). It also runs under later Java versions."

You need to install a java runtime environment should you get output such as:
```java: command not found```

### Getting the Saxon xslt/xquery processor
You want the file called SaxonHE9-8-0-14J.zip (or later) for the java version
[Saxon download page for the java version](http://www.saxonica.com/download/java.xml)
Direct download page (you will be asked to download file after a few seconds): https://sourceforge.net/projects/saxon/files/Saxon-HE/9.8/SaxonHE9-8-0-14J.zip/download

### Installing Saxon on your system
Create a folder to install saxon in:
```
cd /usr/local/bin
sudo mkdir -p /usr/local/bin/programs/saxon
```
Change into this folder and extract saxon into it. 
**Note that you need to change the path to SaxonHE9-8-0-14J.zip to match the correct path on your system.**

```
cd /usr/local/bin/programs/saxon/
sudo unzip /home/username/Downloads/SaxonHE9-8-0-14J.zip
```
Now that the saxon processor is installed (/usr/local/bin/programs/saxon/saxon9he.jar), we need a convenient shellscript to invoke it:

### Creating a shellscript to invoke Saxon processor from the commandline
create a file called /usr/local/bin/saxon
```
sudo nano -cw /usr/local/bin/saxon
```
with this content: 
```
#!/bin/bash
java -jar /usr/local/bin/programs/saxon/saxon9he.jar "$@"
```
Make it executable:
```
sudo chmod +x /usr/local/bin/saxon
```
Test it on the commandline:

```
saxon
```
Output similar to this, should be indicating that tings are in working order, and you are then able to process xsl transformations using the saxon processor:

```
No source file name
Saxon-HE 9.8.0.14J from Saxonica
Usage: see http://www.saxonica.com/documentation/index.html#!using-xsl/commandline
Format: net.sf.saxon.Transform options params
Options available: -? -a -catalog -config -cr -diag -dtd -ea -expand -explain -export -ext -im -init -it -jit -l -lib -license -m -nogo -now -o -opt -or -outval -p -quit -r -relocate -repeat -s -sa -scmin -strip -t -T -target -TB -threads -TJ -Tlevel -Tout -TP -traceout -tree -u -val -versionmsg -warnings -x -xi -xmlversion -xsd -xsdversion -xsiloc -xsl -y
Use -XYZ:? for details of option XYZ
Params: 
  param=value           Set stylesheet string parameter
  +param=filename       Set stylesheet document parameter
  ?param=expression     Set stylesheet parameter using XPath
  !param=value          Set serialization parameter
```

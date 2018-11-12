#!/bin/sh
# this command will run an xsl stylesheet that will take the edx course export and create a flattend xml from it
# this will be sendt to standard output
# if you wish to have it in a file, it must be redirected.
# example:
# edx-course-export-visualizer.sh > flattened_course_export.xml
#
# note that the path tho the xsl-file needs to be updated to the path on your system
# note that you need a working java runtime environment
# note that saxon (xslt/xproc) processor needs to be installed and accessible in the path using a 'saxon' command
#
# -it		initial template. Since no xml-document is passed to the stylesheet, provide the starting template to begin processing
# f=$pwd/$1	the parameter "f" will be a folder. If we just call the command edx-course_export-visualizer.sh, then it will be the current folder
# 		if we supply a child folder to the script: e.a. edx-course-export-visualizer.sh FOLDER, then that folder will be used as a parameter
# -xsl:		The full path to the stylesheet doing the transformation
#
# given a folder as a parameter, the xsl-stylesheet will look for the "course.xml" file in the folder and will try to open that file to begin processing, so this file doesn't need to be specified
pwd=$(pwd)
file="course.xml"

appname () {
  echo "edX course export flatten (2018-11-12) from OsloMet - Oslo Metropolitan University"
  echo ""
}

usage () {
  echo ""
  echo "Merge content of an edx course export into a single flattened xml"
  echo "If the content is in a tar.gz archive, first the contents need folder needs to be extracted: tar -xvzf archive.tar.gz"
  echo "\"course.xml\" is the starter xml file in the course export that begins referencing other xml files"
  echo ""
  echo "This script will outut on standard output. Use file redirection to redirect to file: \"command > file\" to replace or \"command >> file\" to append." 
  echo ""
  echo "Usage: "
  echo "if \"course.xml\" is in the current directory, the DIRECTORY parameter is not required, otherwise, specify a folder to look in"
  echo ""
  echo "Examples without and with redirect of standard output: "
  echo "edx-course-export-flatten"
  echo "edx-course-export-flatten DIRECTORY"
  echo "edx-course-export-flatten > result.xml"
  echo "edx-course-export-flatten DIRECTORY > result.xml"
}

if [ "$#" -eq 0 ]; then
  folderpath="$pwd"
  filepath="$folderpath/$file"
  if [ -f "$file" ]
  then
    saxon -it:main f=$folderpath -xsl:'/usr/local/bin/open-edx-course-export-tools/edx-course-export-flatten.xsl'
  else
    appname
    echo "NOTICE: Could not find \"$file\" in \"$folderpath\" (the current directory)"
    usage
    exit 1
  fi
fi

if [ "$#" -eq 1 ]
then
  folderpath="$pwd/$1"
  filepath="$folderpath/$file"
  if [ -d "$1" ]
  then
    if [ -f "$filepath" ]
    then
#      echo "found $file in $1" >&2
      saxon -it:main f=$folderpath -xsl:'/usr/local/bin/open-edx-course-export-tools/edx-course-export-flatten.xsl'
    else
      appname
      echo "NOTICE: Could not find \"$file\" in \"$folderpath\"" >&2
      usage
    fi
    exit 1
  fi
fi

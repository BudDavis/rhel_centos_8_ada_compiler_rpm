# rhel_centos_8_ada_compiler_rpm

"ada compiler rhel8" "rhel8 ada" "centos8 gnat rpm"  "rhel8 gnat 32 bit rpm"

ada is no longer provided by redhat in rpm format.  

**update:  found this link
https://rpmfind.net/linux/rpm2html/search.php?query=gcc-gnat
obvious, isn't it.**


at first glance, it is as simple as setting the 'build_ada' flag to 1 in the spec file
and rebuilding.  i found it to be a rather daunting process.

32 bit ada works.  the others, not tested.  (looking at the spec file, i think not)

this source rpm should rebuild provided all the pre-reqs are installed.  and these
binaries are installed for the bootstrap ada compiler.

this is not a 'profiled bootstrap build'.  it had some errors that i did not feel
like dealing with.  this may make you nervous, if so, you are welcome to dig in.



To install:

1. install "GUI Server"
   
2. install yum utils          

              `yum install yum-utils`

3. enable this rep

              `vi /etc/yum.repos.d/CentOS-PowerTools.repo 

4.  update the system

               `yum update`
               
5. Install headers (to include 32 bit)

               `yum install glibc-devel-2.28-101.el8.i686 glibc-common glibc-headers`
                
6. Install the redhat compilers

               `yum install "Development Tools"`

7.  Get rid of a package I don't like

               `rpm --erase gcc-gdb-plugin`
               
8.  Install the new compiler(s)             
 
                `rpm --install -Uvh
                       cpp-8.3.1-5.el8.0.3.x86_64.rpm\
                       gcc-8.3.1-5.el8.0.3.x86_64.rpm\
                       gcc-c++-8.3.1-5.el8.0.3.x86_64.rpm\
                       gcc-gfortran-8.3.1-5.el8.0.3.x86_64.rpm\
                       gcc-gnat-8.3.1-5.el8.0.3.x86_64.rpm\
                       libasan-8.3.1-5.el8.0.3.x86_64.rpm\
                       libasan-static-8.3.1-5.el8.0.3.x86_64.rpm
                       libatomic-8.3.1-5.el8.0.3.x86_64.rpm\
                       libatomic-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgcc-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgfortran-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgfortran-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgnat-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgnat-devel-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgnat-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libgomp-8.3.1-5.el8.0.3.x86_64.rpm\
                       libitm-8.3.1-5.el8.0.3.x86_64.rpm\
                       libitm-devel-8.3.1-5.el8.0.3.x86_64.rpm\
                       libitm-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       liblsan-8.3.1-5.el8.0.3.x86_64.rpm\
                       liblsan-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libquadmath-8.3.1-5.el8.0.3.x86_64.rpm\
                       libquadmath-devel-8.3.1-5.el8.0.3.x86_64.rpm\
                       libquadmath-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libstdc++-8.3.1-5.el8.0.3.x86_64.rpm\
                       libstdc++-devel-8.3.1-5.el8.0.3.x86_64.rpm\
                       libstdc++-docs-8.3.1-5.el8.0.3.x86_64.rpm\
                       libstdc++-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libtsan-8.3.1-5.el8.0.3.x86_64.rpm\
                       libtsan-static-8.3.1-5.el8.0.3.x86_64.rpm\
                       libubsan-8.3.1-5.el8.0.3.x86_64.rpm\
                       libubsan-static-8.3.1-5.el8.0.3.x86_64.rpm`
                       
 #9. Test Program  (cut and paste into a.adb)
 
 `with text_io;`
 
 ` procedure a is`
 
 ` begin`
 
 `    text_io.put_line("hello");`
 
  `end;`
 
#10.  Try it

`gnatmake -m32 a.adb`

`file ./a`

`./a`

or

`gnatmake a.adb`

`file ./a`

`./a`

 
#10.  The changes to the spec file is in the file "pfile".  Not proud of it, but it is what it is.

 

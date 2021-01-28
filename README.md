# rhel_centos_8_ada_compiler_rpm

"ada compiler rhel8" "rhel8 ada" "centos8 gnat rpm"  "rhel8 gnat 32 bit rpm"

ada is no longer provided by redhat in rpm format.  

These rpms are provided for situations where you want to keep the gcc version the same as original


| OS version       | gcc version   |
| ---------------- | ------------- |
| CentOS-8.1.1911  | gcc-8.3.1.4   |
| CentOS-8.2.2004  | gcc-8.3.1.5   |
| CentOS-8.3.2011  |               |
| CentOS-Stream-8  | "who knows!"  |


8.2.2004 is the example system
`$ rpm --query gcc`
`gcc-8.3.1-5.1.el8.x86_64`

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

               `yum groupinstall "Development Tools"`

7. The ada packages have exactly the same names as the rest of the gcc package.  So, we need to make sure the versions match exactly.

               `rpm --query gcc`

Keep the results availible.  They must match.

8.  Install the ada rpms.  The below example shows selecting the correct versions with a wildcard.

               `cd <where the rpms are>`
               `yum localinstall *8.3.1-5*`

9. Test Program  (cut and paste into a.adb)
 
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

 
#11.  The changes to the spec file is in the file "pfile".  Not proud of it, but it is what it is.

Note:  The above ada compiler was built without running the testsuite (because I didn't change any functional source) and it was not 'bootstrapped'.  It should work fine, but it is a little different than one would expect.


So, do you trust some random rpm from the internet?  Probably not the smartest thing to do.  You may want to build this for yourself; the problem is that building gcc-gnat requires having an ada compiler.  And that has to come from somewhere.  

No matter what, you have to trust something.

My suggestion is to install the binary rpms for the version you need, and then follow the below suggestions.  They are not verified, but they should get you close to being able to compile these from source.



     `cd SRPMS`
     `rpm --install gcc-8.3.1-5.el8.0.3.src.rpm`  
     `cd`
     `cd rpmbuild`
     `cd SPECS`
     `yum install zlib-devel`
     `yum install dblatex`
     `yum install dejagnu doxygen elfutils-devel elfutils-libelf-devel glibc-static  gmp-devel isl-devel libmpc-devel mpfr-devel python3-devel`
     `yum install python3-sphinx sharutils systemtap`
     `yum install systemtap-sdt-devel texinfo-tex`
     `rpmbuild -ba gcc.spec`


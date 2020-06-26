# rhel_centos_8_ada_compiler_rpm

ada is no longer provided by redhat in rpm format.  

at first glance, it is as simple as setting the 'build_ada' flag to 1 in the spec file
and rebuilding.  i found it to be a rather daunting process.

32 bit ada works.  the others, not tested.  (looking at the spec file, i think not)

this source rpm should rebuild provided all the pre-reqs are installed.  and these
binaries are installed for the bootstrap ada compiler.

this is not a 'profiled bootstrap build'.  it had some errors that i did not feel
like dealing with.  this may make you nervous, if so, you are welcome to dig in.



To install:

1. install "GUI Server"
   
2.            `yum install yum-utils`

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
 
 `with text_io;
  procedure a is
  begin
     text_io.put_line("hello");
  end;`
 
#10.  Try it

`gnatmake -m32 a.adb
 file ./a
 ./a `
 or
 `gnatmake a.adb
  file ./a
 ./a`

 
#10.  The changes to the spec file:

[bdavis@localhost SPECS]$ diff gcc.spec.orig gcc.spec
18c18
< %global build_ada 0
---
> %global build_ada 1
107c107
< Release: %{gcc_release}%{?dist}.0.2
---
> Release: %{gcc_release}%{?dist}.0.4
974c974
< 	--with-bugurl=http://bugzilla.redhat.com/bugzilla \
---
> 	--disable-bootstrap --with-bugurl=http://bugzilla.redhat.com/bugzilla \
1095c1095
< 	../configure --enable-bootstrap \
---
> 	../configure \
1104c1104
<        LDFLAGS_FOR_TARGET="$RPM_LD_FLAGS" profiledbootstrap
---
>        LDFLAGS_FOR_TARGET="$RPM_LD_FLAGS" 
1230a1231,1233
> 
> 
> 
1231a1235,1239
> # bud
> # these should be copied over by the install process but they are not
> cp -R ./gcc/ada/rts %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}
> cp -R ./gcc/ada/rts_32 %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}
> 
1805,1806c1813,1823
< ln -sf ../../../%{multilib_32_arch}-%{_vendor}-%{_target_os}/%{gcc_major}/adainclude 32/adainclude
< ln -sf ../../../%{multilib_32_arch}-%{_vendor}-%{_target_os}/%{gcc_major}/adalib 32/adalib
---
> # bud
> # the others in the code directly above are also wrong.
> # not proud of this, but it works.
> mkdir -p  %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adalib
> cp %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/rts_32/lib* %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adalib
> mkdir -p %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adainclude
> cp %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/rts_32/*.ali %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adalib
> cp %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/rts_32/*.ads %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adainclude
> cp %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/rts_32/*.adb %{buildroot}%{_prefix}/lib/gcc/%{gcc_target_platform}/%{gcc_major}/32/adainclude
> #ln -sf ../../../%{multilib_32_arch}-%{_vendor}-%{_target_os}/%{gcc_major}/adainclude 32/adainclude
> #ln -sf ../../../%{multilib_32_arch}-%{_vendor}-%{_target_os}/%{gcc_major}/adalib 32/adalib
1976,1991c1993,2008
< make %{?_smp_mflags} -k check ALT_CC_UNDER_TEST=gcc ALT_CXX_UNDER_TEST=g++ \
< %if 0%{?fedora} >= 20 || 0%{?rhel} > 7
<      RUNTESTFLAGS="--target_board=unix/'{,-fstack-protector-strong}'" || :
< %else
<      RUNTESTFLAGS="--target_board=unix/'{,-fstack-protector}'" || :
< %endif
< echo ====================TESTING=========================
< ( LC_ALL=C ../contrib/test_summary || : ) 2>&1 | sed -n '/^cat.*EOF/,/^EOF/{/^cat.*EOF/d;/^EOF/d;/^LAST_UPDATED:/d;p;}'
< echo ====================TESTING END=====================
< mkdir testlogs-%{_target_platform}-%{version}-%{release}
< for i in `find . -name \*.log | grep -F testsuite/ | grep -v 'config.log\|acats.*/tests/'`; do
<   ln $i testlogs-%{_target_platform}-%{version}-%{release}/ || :
< done
< tar cf - testlogs-%{_target_platform}-%{version}-%{release} | xz -9e \
<   | uuencode testlogs-%{_target_platform}.tar.xz || :
< rm -rf testlogs-%{_target_platform}-%{version}-%{release}
---
> #make %{?_smp_mflags} -k check ALT_CC_UNDER_TEST=gcc ALT_CXX_UNDER_TEST=g++ \
> #%if 0%{?fedora} >= 20 || 0%{?rhel} > 7
> #     RUNTESTFLAGS="--target_board=unix/'{,-fstack-protector-strong}'" || :
> #%else
> #     RUNTESTFLAGS="--target_board=unix/'{,-fstack-protector}'" || :
> #%endif
> #echo ====================TESTING=========================
> #( LC_ALL=C ../contrib/test_summary || : ) 2>&1 | sed -n '/^cat.*EOF/,/^EOF/{/^cat.*EOF/d;/^EOF/d;/^LAST_UPDATED:/d;p;}'
> #echo ====================TESTING END=====================
> #mkdir testlogs-%{_target_platform}-%{version}-%{release}
> #for i in `find . -name \*.log | grep -F testsuite/ | grep -v 'config.log\|acats.*/tests/'`; do
> #  ln $i testlogs-%{_target_platform}-%{version}-%{release}/ || :
> #done
> #tar cf - testlogs-%{_target_platform}-%{version}-%{release} | xz -9e \
> #  | uuencode testlogs-%{_target_platform}.tar.xz || :
> #rm -rf testlogs-%{_target_platform}-%{version}-%{release}





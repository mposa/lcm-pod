
set (CMAKE_JAVA_TARGET_OUTPUT_NAME lcm)  # todo: remove this when cmake min version >= 2.8.12
# from UseJava.cmake
# In CMake < 2.8.12, add_jar used variables which were set prior to calling
# add_jar for customizing the behavior of add_jar. In order to be backwards
# compatible, check if any of those variables are set, and use them to
# initialize values of the named arguments. (Giving the corresponding named
# argument will override the value set here.)

set(jchart2d_src_dir ${CMAKE_CURRENT_SOURCE_DIR}/jchart2d-code/src)
set(jide_jar ${CMAKE_CURRENT_SOURCE_DIR}/jchart2d-code/ext/jide-oss-2.9.7.jar)
set(xmlgraphics_jar ${CMAKE_CURRENT_SOURCE_DIR}/jchart2d-code/ext/xmlgraphics-commons-1.3.1.jar)

FILE(GLOB_RECURSE lcm_java_files lcm/*.java)
FILE(GLOB_RECURSE jchart2d_files ${jchart2d_src_dir}/*.java)
MESSAGE("Java Files: ${lcm_java_files}")

add_jar(lcm-java
    SOURCES ${lcm_java_files} ${jchart2d_files}
    INCLUDE_JARS ${jide_jar} ${xmlgraphics_jar}
    OUTPUT_NAME lcm)

pods_install_jars(lcm-java)

pods_install_pkg_config_file(lcm-java
    CLASSPATH lcm
    VERSION ${LCM_VERSION})

if (APPLE)
  set(MAC_JAVA_FIX "-Djava.net.preferIPv4Stack=true")
endif()

file(WRITE ${CMAKE_BINARY_DIR}/lcm-logplayer-gui
	"#!/bin/sh\n"
	"exec java -server ${MAC_JAVA_FIX} -Xincgc -Xmx64m -Xms32m -ea -cp \"${CMAKE_INSTALL_PREFIX}/share/java/*\" lcm.logging.LogPlayer \$*\n")
file(INSTALL ${CMAKE_BINARY_DIR}/lcm-logplayer-gui
	     DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
	     FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

file(WRITE ${CMAKE_BINARY_DIR}/lcm-spy
	"#!/bin/sh\n"
	"exec java -server ${MAC_JAVA_FIX} -Xincgc -Xmx128m -Xms64m -ea -cp \"${CMAKE_INSTALL_PREFIX}/share/java/*\" lcm.spy.Spy \$*\n")
file(INSTALL ${CMAKE_BINARY_DIR}/lcm-spy
	     DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
	     FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

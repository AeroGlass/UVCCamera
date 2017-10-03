cmake_minimum_required(VERSION 3.4.1)

include(${COMMON_SCRIPT})

set(UVC_CAMERA_SOURCE ${SOURCE_DIR}/UVCCamera)
set(LIBUSB_SOURCE ${SOURCE_DIR}/libusb)
set(LIBUVC_SOURCE ${SOURCE_DIR}/libuvc)
set(LIBJPEG_SOURCE ${SOURCE_DIR}/libjpeg)
set(RAPIDJSON_SOURCE ${SOURCE_DIR}/rapidjson)

find_library(log-lib log)

# libusb
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DLOG_NDEBUG -DACCESS_RAW_DESCRIPTORS -DANDROID_NDK")

include_directories(${NDK_PLATFORM_INCLUDE_DIR}
					${LIBUSB_SOURCE}/libusb
					${LIBUSB_SOURCE}/libusb/os)

file(GLOB LIBUSB_SRC_FILES ${LIBUSB_SOURCE}/libusb/*.cpp ${LIBUSB_SOURCE}/libusb/*.c)

set(LIBUSB_ANDROID_SRC_FILES
				${LIBUSB_SOURCE}/libusb/os/android_usbfs.c
				${LIBUSB_SOURCE}/libusb/os/poll_posix.c
				${LIBUSB_SOURCE}/libusb/os/threads_posix.c
				${LIBUSB_SOURCE}/libusb/os/android_netlink.c)

add_library(usb100 SHARED ${LIBUSB_SRC_FILES} ${LIBUSB_ANDROID_SRC_FILES})

target_link_libraries(usb100 ${log-lib})

#libjpeg
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DAVOID_TABLES -DANDROID_NDK")

include_directories(${NDK_PLATFORM_INCLUDE_DIR}
					${LIBJPEG_SOURCE})

set(LIBJPEG_SRC_FILES ${LIBJPEG_SOURCE}/ckconfig.c 
		${LIBJPEG_SOURCE}/jaricom.c
		${LIBJPEG_SOURCE}/jcapimin.c
		${LIBJPEG_SOURCE}/jcapistd.c
		${LIBJPEG_SOURCE}/jcarith.c
		${LIBJPEG_SOURCE}/jccoefct.c
		${LIBJPEG_SOURCE}/jccolor.c
		${LIBJPEG_SOURCE}/jcdctmgr.c
		${LIBJPEG_SOURCE}/jchuff.c
		${LIBJPEG_SOURCE}/jcinit.c
		${LIBJPEG_SOURCE}/jcmainct.c
		${LIBJPEG_SOURCE}/jcmarker.c
		${LIBJPEG_SOURCE}/jcmaster.c
		${LIBJPEG_SOURCE}/jcomapi.c
		${LIBJPEG_SOURCE}/jcparam.c
		${LIBJPEG_SOURCE}/jcprepct.c
		${LIBJPEG_SOURCE}/jcsample.c
		${LIBJPEG_SOURCE}/jctrans.c
		${LIBJPEG_SOURCE}/jdapimin.c
		${LIBJPEG_SOURCE}/jdapistd.c
		${LIBJPEG_SOURCE}/jdarith.c
		${LIBJPEG_SOURCE}/jdatadst.c
		${LIBJPEG_SOURCE}/jdatasrc.c
		${LIBJPEG_SOURCE}/jdcoefct.c
		${LIBJPEG_SOURCE}/jdcolor.c
		${LIBJPEG_SOURCE}/jddctmgr.c
		${LIBJPEG_SOURCE}/jdhuff.c
		${LIBJPEG_SOURCE}/jdinput.c
		${LIBJPEG_SOURCE}/jdmainct.c
		${LIBJPEG_SOURCE}/jdmarker.c
		${LIBJPEG_SOURCE}/jdmaster.c
		${LIBJPEG_SOURCE}/jdmerge.c
		${LIBJPEG_SOURCE}/jdpostct.c
		${LIBJPEG_SOURCE}/jdsample.c
		${LIBJPEG_SOURCE}/jdtrans.c
		${LIBJPEG_SOURCE}/jerror.c
		${LIBJPEG_SOURCE}/jfdctflt.c
		${LIBJPEG_SOURCE}/jfdctfst.c
		${LIBJPEG_SOURCE}/jfdctint.c
		${LIBJPEG_SOURCE}/jidctflt.c
		${LIBJPEG_SOURCE}/jidctfst.c 
		${LIBJPEG_SOURCE}/jidctint.c
		${LIBJPEG_SOURCE}/jmemmgr.c
		${LIBJPEG_SOURCE}/jmemansi.c
		${LIBJPEG_SOURCE}/jquant1.c
		${LIBJPEG_SOURCE}/jquant2.c
		${LIBJPEG_SOURCE}/jutils.c
		${LIBJPEG_SOURCE}/wrbmp.c)

add_library(jpeg STATIC ${LIBJPEG_SRC_FILES})

# libuvc
include_directories(${NDK_PLATFORM_INCLUDE_DIR}
					${LIBUVC_SOURCE}/inlcude)

file(GLOB LIBUVC_SRC_FILES ${LIBUVC_SOURCE}/src/*.cpp ${LIBUVC_SOURCE}/src/*.c)

add_library(uvc SHARED ${LIBUVC_SRC_FILES})

target_link_libraries(uvc ${log-lib} jpeg usb100)

# UVCCamera
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

include_directories(${NDK_PLATFORM_INCLUDE_DIR}
					${SOURCE_DIR}
					${UVC_CAMERA_SOURCE}
					${LIBUVC_SOURCE}/include
					${LIBUVC_SOURCE}/include/libuvc
					${LIBUSB_SOURCE}
					${LIBUSB_SOURCE}/libusb
					${RAPIDJSON_SOURCE}/include
					)
					
file(GLOB UVC_CAMERA_SRC_FILES ${UVC_CAMERA_SOURCE}/*.cpp ${UVC_CAMERA_SOURCE}/*.c)

add_library(UVCCamera SHARED ${UVC_CAMERA_SRC_FILES})

find_library(android-lib android)
find_library(jnigraphics-lib jnigraphics)

target_link_libraries(UVCCamera
						${android-lib}
						${log-lib}
						${jnigraphics-lib}
						usb100
						uvc
					)

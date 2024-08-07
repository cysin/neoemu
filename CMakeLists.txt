cmake_minimum_required(VERSION 3.5)
#set(CMAKE_VERBOSE_MAKEFILE ON)

project(pfbneo)

find_package(SDL2 REQUIRED)

if(SDL2_FOUND)
    include_directories(${SDL2_INCLUDE_DIRS})

else()
    message(FATAL_ERROR "SDL2 not found")
endif()

# find_package(SDL2_image REQUIRED)


# if(SDL2_IMAGE_FOUND)
    # include_directories(${SDL2_IMAGE_INCLUDE_DIRS})

# else()
    # message(FATAL_ERROR "SDL2 image not found")
# endif()

# pfbneo became very heavy in size, we disable consoles for ps vita
option(OPTION_LIGHT "Disable consoles and some arcade drivers" OFF)
# add_subdirectory(external/libcross2d)
# handle m68k
FILE(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/deps)
ADD_CUSTOM_COMMAND(OUTPUT
        ${CMAKE_CURRENT_BINARY_DIR}/deps/m68kops.h ${CMAKE_CURRENT_BINARY_DIR}/deps/m68kops.c
        COMMAND gcc ${CMAKE_SOURCE_DIR}/cpu/m68k/m68kmake.c -o ${CMAKE_CURRENT_BINARY_DIR}/deps/m68kmake
        COMMAND ${CMAKE_CURRENT_BINARY_DIR}/deps/m68kmake ${CMAKE_CURRENT_BINARY_DIR}/deps/ ${CMAKE_SOURCE_DIR}/cpu/m68k/m68k_in.c)

##############
# burn
##############
set(BURN_DIRS
        ${CMAKE_SOURCE_DIR}/burn
        ${CMAKE_SOURCE_DIR}/burn/devices
        ${CMAKE_SOURCE_DIR}/burn/snd
)
file(GLOB SRC_BURN
        ${CMAKE_SOURCE_DIR}/burn/*.c*
        ${CMAKE_SOURCE_DIR}/burn/devices/*.c*
        ${CMAKE_SOURCE_DIR}/burn/snd/*.c*
)
##############
# burner
##############
set(BURNER_DIRS
        ${CMAKE_SOURCE_DIR}/burner
        ${CMAKE_SOURCE_DIR}/burner/sdl
)
file(GLOB SRC_BURNER
        ${CMAKE_SOURCE_DIR}/burner/*.c*
        ${CMAKE_SOURCE_DIR}/burner/sdl/*.c*
        # ${CMAKE_SOURCE_DIR}/burner/sdl/neocdlist.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/stringset.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/ips_manager.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/romdata.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/localise_gamelist.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/replay.cpp
        # ${CMAKE_SOURCE_DIR}/burner/sdl/main.cpp
)
# list(REMOVE_ITEM SRC_BURNER
#         ${CMAKE_SOURCE_DIR}/burner/conc.cpp
#         ${CMAKE_SOURCE_DIR}/burner/cong.cpp
#         ${CMAKE_SOURCE_DIR}/burner/dat.cpp
#         ${CMAKE_SOURCE_DIR}/burner/gamc.cpp
#         ${CMAKE_SOURCE_DIR}/burner/gami.cpp
#         ${CMAKE_SOURCE_DIR}/burner/image.cpp
#         ${CMAKE_SOURCE_DIR}/burner/ioapi.c
#         ${CMAKE_SOURCE_DIR}/burner/sshot.cpp
#         ${CMAKE_SOURCE_DIR}/burner/unzip.c
#         ${CMAKE_SOURCE_DIR}/burner/zipfn.cpp
#         ${CMAKE_SOURCE_DIR}/burner/luaengine.cpp
#         ${CMAKE_SOURCE_DIR}/burner/luasav.cpp
# )
# Print the list as a space-separated string
message(STATUS "SRC_BURNER: ${SRC_BURNER}")

##############
# drivers
##############
set(DRV_DIRS
        ${CMAKE_SOURCE_DIR}/burn/drv
        # ${CMAKE_SOURCE_DIR}/burn/drv/atari
        ${CMAKE_SOURCE_DIR}/burn/drv/capcom
        # ${CMAKE_SOURCE_DIR}/burn/drv/cave
        # ${CMAKE_SOURCE_DIR}/burn/drv/cps3
        # ${CMAKE_SOURCE_DIR}/burn/drv/dataeast
        # ${CMAKE_SOURCE_DIR}/burn/drv/galaxian
        # ${CMAKE_SOURCE_DIR}/burn/drv/irem
        # ${CMAKE_SOURCE_DIR}/burn/drv/konami
        # ${CMAKE_SOURCE_DIR}/burn/drv/midway
        # ${CMAKE_SOURCE_DIR}/burn/drv/pst90s
        # ${CMAKE_SOURCE_DIR}/burn/drv/pre90s
        # ${CMAKE_SOURCE_DIR}/burn/drv/neogeo
        # ${CMAKE_SOURCE_DIR}/burn/drv/pgm
        # ${CMAKE_SOURCE_DIR}/burn/drv/psikyo
        # ${CMAKE_SOURCE_DIR}/burn/drv/sega
        # ${CMAKE_SOURCE_DIR}/burn/drv/taito
        # ${CMAKE_SOURCE_DIR}/burn/drv/toaplan
)

# if (NOT OPTION_LIGHT)
#     list(APPEND DRV_DIRS
#             ${CMAKE_SOURCE_DIR}/burn/drv/channelf
#             ${CMAKE_SOURCE_DIR}/burn/drv/coleco
#             ${CMAKE_SOURCE_DIR}/burn/drv/megadrive
#             ${CMAKE_SOURCE_DIR}/burn/drv/pce
#             ${CMAKE_SOURCE_DIR}/burn/drv/nes
#             ${CMAKE_SOURCE_DIR}/burn/drv/sg1000
#             ${CMAKE_SOURCE_DIR}/burn/drv/sms
#             ${CMAKE_SOURCE_DIR}/burn/drv/msx
#             ${CMAKE_SOURCE_DIR}/burn/drv/spectrum
#     )
# endif ()

foreach (DIR ${DRV_DIRS})
    file(GLOB DRV ${DIR}/*.cpp)
    list(APPEND SRC_DRV ${DRV})
endforeach ()

list(REMOVE_ITEM SRC_DRV
        ${CMAKE_SOURCE_DIR}/burn/drv/capcom/ctv_make.cpp
        # ${CMAKE_SOURCE_DIR}/burn/drv/pgm/pgm_sprite_create.cpp
)

##############
# interfaces
##############
set(INTF_DIRS
        ${CMAKE_SOURCE_DIR}/intf
        ${CMAKE_SOURCE_DIR}/intf/input
        ${CMAKE_SOURCE_DIR}/intf/input/sdl
        ${CMAKE_SOURCE_DIR}/intf/cd
        ${CMAKE_SOURCE_DIR}/intf/cd/sdl
        ${CMAKE_SOURCE_DIR}/intf/audio
        ${CMAKE_SOURCE_DIR}/intf/audio/sdl
        ${CMAKE_SOURCE_DIR}/intf/video
        ${CMAKE_SOURCE_DIR}/intf/video/sdl
)
file(GLOB SRC_INTF
        ${CMAKE_SOURCE_DIR}/intf/*.cpp
        ${CMAKE_SOURCE_DIR}/intf/audio/*.c*
        ${CMAKE_SOURCE_DIR}/intf/audio/sdl/*.c*
        ${CMAKE_SOURCE_DIR}/intf/cd/*.c*
        ${CMAKE_SOURCE_DIR}/intf/cd/sdl/*.c*
        ${CMAKE_SOURCE_DIR}/intf/input/*.c*
        ${CMAKE_SOURCE_DIR}/intf/input/sdl/*.c*
        ${CMAKE_SOURCE_DIR}/intf/video/*.c*
        ${CMAKE_SOURCE_DIR}/intf/video/sdl/*.c*
)
# list(REMOVE_ITEM SRC_INTF
#         ${CMAKE_SOURCE_DIR}/intf/audio/aud_dsp.cpp
#         ${CMAKE_SOURCE_DIR}/intf/audio/aud_interface.cpp
# )

##############
# cpu
##############
set(CPU_DIRS
        ${CMAKE_SOURCE_DIR}/cpu
        #${CMAKE_SOURCE_DIR}/cpu/a68k
        # ${CMAKE_SOURCE_DIR}/cpu/adsp2100
        # ${CMAKE_SOURCE_DIR}/cpu/arm
        # ${CMAKE_SOURCE_DIR}/cpu/arm7
        # ${CMAKE_SOURCE_DIR}/cpu/e132xs
        # ${CMAKE_SOURCE_DIR}/cpu/f8
        # ${CMAKE_SOURCE_DIR}/cpu/h6280
        # ${CMAKE_SOURCE_DIR}/cpu/hd6309
        # ${CMAKE_SOURCE_DIR}/cpu/i8x41
        # ${CMAKE_SOURCE_DIR}/cpu/i386
        # ${CMAKE_SOURCE_DIR}/cpu/i8039
        # ${CMAKE_SOURCE_DIR}/cpu/i8051
        # ${CMAKE_SOURCE_DIR}/cpu/konami
        ${CMAKE_SOURCE_DIR}/cpu/m68k
        # ${CMAKE_SOURCE_DIR}/cpu/m377
        # ${CMAKE_SOURCE_DIR}/cpu/m6502
        # ${CMAKE_SOURCE_DIR}/cpu/m6800
        # ${CMAKE_SOURCE_DIR}/cpu/m6805
        # ${CMAKE_SOURCE_DIR}/cpu/m6809
        # ${CMAKE_SOURCE_DIR}/cpu/mips3
        # ${CMAKE_SOURCE_DIR}/cpu/nec
        # ${CMAKE_SOURCE_DIR}/cpu/pic16c5x
        # ${CMAKE_SOURCE_DIR}/cpu/s2650
        # ${CMAKE_SOURCE_DIR}/cpu/sh2
        # ${CMAKE_SOURCE_DIR}/cpu/sh4
        # ${CMAKE_SOURCE_DIR}/cpu/tlcs90
        # ${CMAKE_SOURCE_DIR}/cpu/tlcs900
        # ${CMAKE_SOURCE_DIR}/cpu/tms34
        # ${CMAKE_SOURCE_DIR}/cpu/tms32010
        #${CMAKE_SOURCE_DIR}/cpu/tms34010
        # ${CMAKE_SOURCE_DIR}/cpu/upd7725
        # ${CMAKE_SOURCE_DIR}/cpu/upd7810
        # ${CMAKE_SOURCE_DIR}/cpu/v60
        ${CMAKE_SOURCE_DIR}/cpu/z80
        # ${CMAKE_SOURCE_DIR}/cpu/z180
)
file(GLOB_RECURSE SRC_CPU
        ${CMAKE_SOURCE_DIR}/cpu/*.cpp
        ${CMAKE_SOURCE_DIR}/cpu/m68k/m68kcpu.c
)
# list(REMOVE_ITEM SRC_CPU
#         ${CMAKE_SOURCE_DIR}/cpu/tms34010_intf.cpp
#         ${CMAKE_SOURCE_DIR}/cpu/tms34010/tms34010_newdasm.cpp
#         ${CMAKE_SOURCE_DIR}/cpu/tms34010/tms34010_dasm.cpp
#         ${CMAKE_SOURCE_DIR}/cpu/mips3/x64/mips3_x64.cpp
# )

# if (OPTION_LIGHT)
#     # devices
#     list(FILTER SRC_BURN EXCLUDE REGEX
#             epic12.cpp|serflash.cpp|bsmt2000.cpp|mb87078.cpp|poly.cpp|sknsspr.cpp|qs1000.cpp|v3021.cpp|decobsmt.cpp|intelfsh.cpp|st0020.cpp|vdc.cpp|ds2404.cpp|rtc9701.cpp)
#     # snd
#     list(FILTER SRC_BURN EXCLUDE REGEX
#             burn_ymf271.cpp|c352.cpp|c6280.cpp|es5506.cpp|i5000.cpp|ics2115.cpp|iremga20.cpp|mpeg_audio.cpp|multipcm.cpp|ymf271.cpp|ymz770.cpp)
#     # cpu
#     list(FILTER SRC_CPU EXCLUDE REGEX
#             adsp2100_intf.cpp|arm_intf.cpp|arm7_intf.cpp|h6280_intf.cpp|mips3_intf.cpp|pic16c5x_intf.cpp|tms34_intf.cpp|arm.cpp|e132xs.cpp)
#     list(FILTER SRC_CPU EXCLUDE REGEX
#             h6280.cpp|m37710.cpp|cop0.cpp|cop1.cpp|mips3.cpp|pic16c5x.cpp|sh4.cpp|tms34010.cpp|upd7725.cpp|v60.cpp|arm7.cpp|adsp2100.cpp|i386.cpp)
#     list(FILTER SRC_CPU EXCLUDE REGEX mips3_dasm.cpp|tms34010_optable.cpp)
#     # drivers
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_cv1k.cpp|d_decomlc.cpp|d_deco156.cpp|d_simpl156.cpp|d_deco32.cpp|d_backfire.cpp|d_vaportra.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_tumblep.cpp|d_supbtime.cpp|d_funkyjet.cpp|deco16ic.cpp|d_dietgogo.cpp|d_dec0.cpp|d_dassault.cpp|d_darkseal.cpp|d_cninja.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_boogwing.cpp|d_cbuster.cpp|d_actfancr.cpp|d_boogwing.cpp|d_dblewing.cpp|d_dreambal.cpp|d_rohga.cpp|d_sshangha.cpp|d_pktgaldx.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_m92.cpp|d_m107.cpp|d_gijoe.cpp|d_moo.cpp|d_mystwarr.cpp|d_xexex.cpp|d_xmen.cpp|dcs2k.cpp|d_kinst.cpp|d_tunit.cpp|d_wunit.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_xunit.cpp|d_yunit.cpp|ide.cpp|midtunit.cpp|midwayic.cpp|midwunit.cpp|narc_sound.cpp|williams_adpcm.cpp|williams_cvsd.cpp|yawdim_sound.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_msx.cpp|d_nes.cpp|d_pce.cpp|pce.cpp|d_pgm.cpp|pgm_asic25.cpp|pgm_asic27a_type1.cpp|pgm_asic27a_type2.cpp|pgm_asic27a_type3.cpp|pgm_asic3.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             pgm_crypt.cpp|pgm_draw.cpp|pgm_run.cpp|d_exterm.cpp|d_madmotor.cpp|d_mcr68.cpp|d_psikyo4.cpp|d_psikyosh.cpp|psikyosh_render.cpp|d_ms32.cpp|d_ssv.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_namcona1.cpp|d_namconb1.cpp|d_btoads.cpp|d_artmagic.cpp|d_eolith16.cpp|d_gstream.cpp|d_vegaeo.cpp|d_limenko.cpp|d_pasha2.cpp|d_eolith.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_x2222.cpp|d_f-32.cpp|d_vamphalf.cpp|d_suprnova.cpp|d_drgnmst.cpp|d_blackt96.cpp|d_seibuspi.cpp|d_kickgoal.cpp|d_playmark.cpp|d_fuukifg3.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_rabbit.cpp|d_itech32.cpp|d_dreamwld.cpp|d_macrossp.cpp|d_jchan.cpp|d_galpani3.cpp|d_mirage.cpp|d_seta2.cpp|d_segas32.cpp|d_sys24.cpp)
#     list(FILTER SRC_DRV EXCLUDE REGEX
#             d_spectrum.cpp|d_slapshot.cpp|d_superchs.cpp|d_taitof3.cpp|d_taitof2.cpp|d_taitoz.cpp|taitof3_snd.cpp|taitof3_video.cpp|d_groundfx.cpp|d_gunbuster.cpp|d_undrfire.cpp|d_galastrm.cpp)
# endif ()

##############
# 7z
##############
set(SPNG_DIRS
        ${CMAKE_SOURCE_DIR}/dep/libs/libspng
)

file(GLOB SRC_SPNG
        ${CMAKE_SOURCE_DIR}/dep/libs/libspng/*.c*
)

set(ZLIB_DIRS
        ${CMAKE_SOURCE_DIR}/dep/libs/zlib
)

file(GLOB SRC_ZLIB
        ${CMAKE_SOURCE_DIR}/dep/libs/zlib/*.c*
)

set(7Z_DIRS
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z
)
file(GLOB SRC_7Z
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zDec.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zArcIn.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zStream.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/LzmaDec.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/Lzma2Dec.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/Bra.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/Bcj2.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zBuf.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/Delta.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/Bra86.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/BraIA64.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zCrc.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/7zCrcOpt.c
        ${CMAKE_SOURCE_DIR}/dep/libs/lib7z/CpuArch.c
)

##############
# pfbneo
##############
# set(PFBA_DIRS sources sources/fbneo)
# file(GLOB SRC_PFBA sources/*.c* sources/fbneo/*.c*)
# list(APPEND SRC_PFBA ../main.cpp)

# includes
set(INC ${PFBA_DIRS}
        ${7Z_DIRS}
        ${BURN_DIRS}
        ${BURNER_DIRS}
        ${DRV_DIRS}
        ${CPU_DIRS}
        ${INTF_DIRS}
        ${SPNG_DIRS}
        ${ZLIB_DIRS}
        ${CMAKE_CURRENT_BINARY_DIR}/deps
)

# flags
set(FLAGS
        -D__PFBA__
        -D__PEMU_VERSION_MAJOR__=${VERSION_MAJOR}
        -D__PEMU_VERSION_MINOR__=${VERSION_MINOR}
        -DLSB_FIRST
        -DINCLUDE_7Z_SUPPORT -DMINIZIP_LIB
        -DBUILD_SDL2 -DBUILD_M68K
        -fomit-frame-pointer -Wno-write-strings
        -Wall -Wno-long-long -Wno-sign-compare -Wno-uninitialized -Wno-unused
        -Wno-conversion -Wno-attributes -Wno-unused-parameter -Wno-unused-value
        -Wno-format-truncation -Wno-parentheses -Wno-sequence-point -Wno-format
        -Wno-misleading-indentation -Wno-restrict -Wno-unused-result
)

if (OPTION_LIGHT)
    list(APPEND FLAGS -D__PFBN_LIGHT__)
endif ()

set(CMAKE_CXX_STANDARD 11)

#################
# PSP2 (ps vita)
#################
# if (PLATFORM_VITA)
#     enable_language(ASM)
#     set(TITLE_ID "PFBN00001" CACHE STRING "" FORCE)
#     list(APPEND INC sources/cpu/cyclone)
#     list(REMOVE_ITEM SRC_CPU ${CMAKE_SOURCE_DIR}/cpu/m68000_intf.cpp)
#     list(APPEND SRC_PFBA sources/cpu/c68000_intf.cpp sources/cpu/cyclone/Cyclone.s)
#     list(APPEND FLAGS -DBUILD_C68K -DUSE_FILE32API -DUSE_SPEEDHACKS -D__PFBA_ARM__)
#     list(APPEND LDFLAGS ScePower_stub pthread)
# elseif (PLATFORM_PS4)
#     set(PS4_PKG_TITLE "pFBN - Portable Final Burn Neo")
#     set(PS4_PKG_TITLE_ID "PFBN00001")
#     set(PS4_PKG_VERSION "${VERSION_MAJOR}.${VERSION_MINOR}")
#     list(APPEND FLAGS -D__fastcall=)
#     list(APPEND LDFLAGS)
# elseif (PLATFORM_PS3)
#     ##############
#     # PS3
#     ##############
#     list(APPEND FLAGS
#             -D__PS3__
#             -DNO_KEYBOARD -D__CELLOS_LV2__ -DUSE_FILE32API
#             -mhard-float -ffunction-sections -fdata-sections)
#     list(APPEND LDFLAGS
#             ${PSL1GHT}/ppu/lib/libpngdec.a
#             ${PSL1GHT}/portlibs/ppu/lib/libfreetype.a
#             ${PSL1GHT}/portlibs/ppu/lib/libpng.a
#             ${PSL1GHT}/portlibs/ppu/lib/libtiff.a
#             ${PSL1GHT}/portlibs/ppu/lib/libjpeg.a
#             ${PSL1GHT}/ppu/lib/libgcm_sys.a
#             ${PSL1GHT}/ppu/lib/librsx.a
#             ${PSL1GHT}/ppu/lib/libsysutil.a
#             ${PSL1GHT}/ppu/lib/libsysmodule.a
#             ${PSL1GHT}/ppu/lib/libio.a
#             ${PSL1GHT}/ppu/lib/libaudio.a
#             ${PSL1GHT}/portlibs/ppu/lib/libz.a)
# elseif (PLATFORM_3DS)
#     ##############
#     # 3DS
#     ##############
#     enable_language(ASM)
#     list(REMOVE_ITEM SRC_CPU ${CMAKE_SOURCE_DIR}/cpu/m68000_intf.cpp)
#     list(APPEND SRC_PFBA sources/cpu/c68000_intf.cpp sources/cpu/cyclone/Cyclone.s)
#     list(APPEND INC
#             sources/3ds
#             sources/cpu/cyclone
#             ${DEVKITPRO}/libctru/include
#             ${DEVKITPRO}/portlibs/armv6k/include)
#     list(APPEND FLAGS -DLSB_FIRST -DUSE_SPEEDHACKS -D__PFBA_ARM__
#             -ffunction-sections -fomit-frame-pointer)
# elseif (PLATFORM_SWITCH)
#     ##############
#     # NX (SWITCH)
#     ##############
#     list(APPEND FLAGS -DUSE_FILE32API -ffunction-sections -fomit-frame-pointer)
# elseif (PLATFORM_LINUX)
    ##############
    # LINUX
    ##############
    list(APPEND FLAGS -D__fastcall=)
# elseif (PLATFORM_WINDOWS)
#     ##############
#     # WINDOWS
#     ##############
#     list(APPEND FLAGS)
# elseif (PLATFORM_ANDROID)
#     if (ANDROID_ABI STREQUAL armeabi-v7a)
#         list(APPEND FLAGS -DUSE_SPEEDHACKS
#                 -ffunction-sections -fomit-frame-pointer
#                 -ffast-math -frounding-math -marm -mcpu=cortex-a7 -mfpu=neon)
#     endif ()
#     list(APPEND FLAGS -O3)
# endif (PLATFORM_VITA)

# source files
set(SRC ${SRC_PFBA} ${SRC_CPU} ${SRC_DRV} ${SRC_BURN} ${SRC_BURNER} ${SRC_INTF} ${SRC_7Z} ${SRC_SPNG} ${SRC_ZLIB})

#####################
# pfbneo executable
#####################
message(STATUS "SRC: ${SRC}")
add_executable(${PROJECT_NAME} ${SRC} ${CMAKE_CURRENT_BINARY_DIR}/deps/m68kops.c)
target_compile_options(${PROJECT_NAME} PRIVATE ${FLAGS})
target_include_directories(${PROJECT_NAME} PRIVATE ${INC})
target_link_libraries(${PROJECT_NAME} ${SDL2_LIBRARIES} ${SDL2_IMAGE_LIBRARIES} SDL2_image GL)
# target_link_libraries(${PROJECT_NAME} cross2dui cross2d ${LDFLAGS})
# include libcross2d targets
# include(${CMAKE_SOURCE_DIR}/external/libcross2d/cmake/targets.cmake)

# add common skin data
# add_custom_target(${PROJECT_NAME}.data.skin
#         COMMAND ${CMAKE_COMMAND} -D SRC=${CMAKE_SOURCE_DIR}/data/common/datadir -D DST=${CMAKE_CURRENT_BINARY_DIR}/data_datadir -P ${CMAKE_SOURCE_DIR}/external/libcross2d/cmake/copy_custom.cmake
#         COMMAND ${CMAKE_COMMAND} -D SRC=${CMAKE_SOURCE_DIR}/data/${TARGET_PLATFORM}/datadir -D DST=${CMAKE_CURRENT_BINARY_DIR}/data_datadir -P ${CMAKE_SOURCE_DIR}/external/libcross2d/cmake/copy_custom.cmake
#         COMMAND ${CMAKE_COMMAND} -D SRC=${CMAKE_SOURCE_DIR}/data/common/romfs -D DST=${CMAKE_CURRENT_BINARY_DIR}/data_romfs -P ${CMAKE_SOURCE_DIR}/external/libcross2d/cmake/copy_custom.cmake
#         COMMAND ${CMAKE_COMMAND} -D SRC=${CMAKE_SOURCE_DIR}/data/${TARGET_PLATFORM}/romfs -D DST=${CMAKE_CURRENT_BINARY_DIR}/data_romfs -P ${CMAKE_SOURCE_DIR}/external/libcross2d/cmake/copy_custom.cmake
# )
# add_dependencies(${PROJECT_NAME}.data.skin ${PROJECT_NAME}.data)
# if (OPTION_LIGHT)
#     add_custom_target(${PROJECT_NAME}.data.light
#             COMMAND rm -rf "${CMAKE_CURRENT_BINARY_DIR}/data_romfs/gamelist*"
#             COMMAND cp -f "${CMAKE_CURRENT_SOURCE_DIR}/data/light/gamelist-light.xml" "${CMAKE_CURRENT_BINARY_DIR}/data_romfs/gamelist.xml"
#     )
#     add_dependencies(${PROJECT_NAME}.data.light ${PROJECT_NAME}.data)
# endif ()
# add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}.data.skin)
# if (OPTION_LIGHT)
#     add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}.data.light)
# endif ()

# fbneo deps
find_package(Perl REQUIRED)
add_custom_target(${PROJECT_NAME}.deps
        COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_CURRENT_BINARY_DIR}/deps
        # ctv.h
        COMMAND g++ ${CMAKE_SOURCE_DIR}/burn/drv/capcom/ctv_make.cpp -o ${CMAKE_CURRENT_BINARY_DIR}/deps/ctv_make
        COMMAND ${CMAKE_CURRENT_BINARY_DIR}/deps/ctv_make > ${CMAKE_CURRENT_BINARY_DIR}/deps/ctv.h
        # pgm sprite
        # COMMAND g++ ${CMAKE_SOURCE_DIR}/burn/drv/pgm/pgm_sprite_create.cpp -o ${CMAKE_CURRENT_BINARY_DIR}/deps/pgm_sprite_create
        # COMMAND ${CMAKE_CURRENT_BINARY_DIR}/deps/pgm_sprite_create > ${CMAKE_CURRENT_BINARY_DIR}/deps/pgm_sprite.h
        # # func headers
        # COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/toa_gp9001_func.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/toa_gp9001_func.h
        # COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/neo_sprite_func.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/neo_sprite_func.h
        # COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/cave_tile_func.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/cave_tile_func.h
        # COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/cave_sprite_func.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/cave_sprite_func.h
        # COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/psikyo_tile_func.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/psikyo_tile_func.h
        # driverlist.h
        COMMAND ${PERL_EXECUTABLE} ${CMAKE_SOURCE_DIR}/dep/scripts/gamelist.pl -o ${CMAKE_CURRENT_BINARY_DIR}/deps/driverlist.h -l ${CMAKE_CURRENT_BINARY_DIR}/deps/gamelist.txt ${SRC_DRV}
)
# if (PLATFORM_ANDROID)
#     add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}.deps)
# endif ()

message("-- C2D: Be sure to build \"${PROJECT_NAME}.deps\" target before \"${PROJECT_NAME}\" target")

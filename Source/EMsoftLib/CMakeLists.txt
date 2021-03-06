project(EMsoftLib)


set(EMsoftLib_SRCS
  #${EMsoftLib_SOURCE_DIR}/apb.f90
  ${EMsoftLib_SOURCE_DIR}/constants.f90
  ${EMsoftLib_SOURCE_DIR}/crystal.f90
  ${EMsoftLib_SOURCE_DIR}/defectmodule.f90
  ${EMsoftLib_SOURCE_DIR}/diffraction.f90
  ${EMsoftLib_SOURCE_DIR}/eispack.f90
  ${EMsoftLib_SOURCE_DIR}/error.f90
  ${EMsoftLib_SOURCE_DIR}/files.f90
  ${EMsoftLib_SOURCE_DIR}/graphics.f90
  ${EMsoftLib_SOURCE_DIR}/gvectors.f90
  ${EMsoftLib_SOURCE_DIR}/io.f90
  ${EMsoftLib_SOURCE_DIR}/kvectors.f90
  ${EMsoftLib_SOURCE_DIR}/initializers.f90
  ${EMsoftLib_SOURCE_DIR}/Lambert.f90
  ${EMsoftLib_SOURCE_DIR}/fftw3mod.f90
  ${EMsoftLib_SOURCE_DIR}/filters.f90
  ${EMsoftLib_SOURCE_DIR}/math.f90
  ${EMsoftLib_SOURCE_DIR}/multibeams.f90
  ${EMsoftLib_SOURCE_DIR}/ECCImod.f90
  ${EMsoftLib_SOURCE_DIR}/MBmodule.f90
  ${EMsoftLib_SOURCE_DIR}/EMdymod.f90
  ${EMsoftLib_SOURCE_DIR}/NameListTypedefs.f90
  ${EMsoftLib_SOURCE_DIR}/NameListHandlers.f90
  ${EMsoftLib_SOURCE_DIR}/JSONsupport.f90
  ${EMsoftLib_SOURCE_DIR}/CLsupport.f90
  ${EMsoftLib_SOURCE_DIR}/Indexingmod.f90
  ${EMsoftLib_SOURCE_DIR}/noise.f90
  ${EMsoftLib_SOURCE_DIR}/others.f90
  ${EMsoftLib_SOURCE_DIR}/pgm.f90
  ${EMsoftLib_SOURCE_DIR}/postscript.f90
  ${EMsoftLib_SOURCE_DIR}/quaternions.f90
  ${EMsoftLib_SOURCE_DIR}/rng.f90
  ${EMsoftLib_SOURCE_DIR}/rotations.f90
  ${EMsoftLib_SOURCE_DIR}/spsort.f
  ${EMsoftLib_SOURCE_DIR}/so3.f90
  ${EMsoftLib_SOURCE_DIR}/povray.f90
  ${EMsoftLib_SOURCE_DIR}/STEMmodule.f90
  ${EMsoftLib_SOURCE_DIR}/symmetry.f90
  ${EMsoftLib_SOURCE_DIR}/tiff.f90
  ${EMsoftLib_SOURCE_DIR}/timing.f90
  ${EMsoftLib_SOURCE_DIR}/typedefs.f90
  ${EMsoftLib_SOURCE_DIR}/bobyqa.f90
  ${EMsoftLib_SOURCE_DIR}/bobyqa_refinement.f90
  ${EMsoftLib_SOURCE_DIR}/dictmod.f90
  ${EMsoftLib_SOURCE_DIR}/distortion.f90
  ${EMsoftLib_SOURCE_DIR}/PEDmod.f90
  ${EMsoftLib_SOURCE_DIR}/MuellerCalculus.f90
  ${EMsoftLib_SOURCE_DIR}/simulated_annealing.f90

  ${EMsoftLib_Additional_SRCS}
)

set(EMsoftLib_C_SRCS
  ${EMsoftLib_SOURCE_DIR}/mbir.c
  ${EMsoftLib_SOURCE_DIR}/mbirHeader.h
  ${EMsoftLib_SOURCE_DIR}/denoise.c
)
if(NOT WIN32 AND BUILD_SHARED_LIBS)
  set_source_files_properties(${EMsoftLib_C_SRCS} PROPERTIES COMPILE_FLAGS -fPIC)
endif()

if(EMsoft_ENABLE_TESTING)
  set(EMsoft_TESTING_DIR "${EMsoft_BINARY_DIR}/Testing")
  file(MAKE_DIRECTORY ${EMsoft_TESTING_DIR})
endif()

configure_file(${EMsoftLib_SOURCE_DIR}/local.f90.in 
         ${EMsoftLib_BINARY_DIR}/local.f90 @ONLY)

set(EMsoftLib_SRCS
  ${EMsoftLib_SRCS}
  ${EMsoftLib_BINARY_DIR}/local.f90  # NOTE THIS FILE IS GENERATED DURING CMAKE TIME
)


set(install_dir "bin")
set(lib_install_dir "lib")
if(WIN32)
  set(install_dir "bin")
  set(lib_install_dir "lib")
elseif(APPLE)
  set(install_dir "bin")
  set(lib_install_dir "lib")
endif()


include_directories(${FFTW3_INCLUDE_DIR})
include_directories(${CLFORTRAN_INCLUDE_DIR})

add_library(EMsoftLib_C STATIC ${EMsoftLib_C_SRCS})
set_target_properties (EMsoftLib_C PROPERTIES
  LINKER_LANGUAGE Fortran
   # Fortran_MODULE_DIRECTORY ${CMAKE_Fortran_MODULE_DIRECTORY}
  FOLDER EMsoftPublic
)

ADD_LIBRARY(EMsoftLib ${LIB_TYPE} ${EMsoftLib_SRCS})
TARGET_LINK_LIBRARIES(EMsoftLib EMsoftLib_C jsonfortran ${FFTW3_LIBRARY} ${OpenCL_LIBRARY} ${EMsoft_BLAS_LAPACK_LIBS})

set_target_properties (EMsoftLib PROPERTIES
  LINKER_LANGUAGE Fortran
   # Fortran_MODULE_DIRECTORY ${CMAKE_Fortran_MODULE_DIRECTORY}
  FOLDER EMsoftPublic
)

if(APPLE AND BUILD_SHARED_LIBS)
  # set_target_properties(EMsoftLib PROPERTIES MACOSX_RPATH TRUE)
  # set_target_properties(EMsoftLib PROPERTIES INSTALL_NAME_DIR "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}")
  # set_target_properties(EMsoftLib PROPERTIES BUILD_WITH_INSTALL_RPATH ON)
endif()


#if(BUILD_SHARED_LIBS)
  INSTALL(TARGETS EMsoftLib EMsoftLib_C
    COMPONENT Applications
    EXPORT EMsoftLibTargets
    RUNTIME DESTINATION ${install_dir}
    LIBRARY DESTINATION ${lib_install_dir}
    ARCHIVE DESTINATION ${lib_install_dir}
    BUNDLE DESTINATION "."
  )
#endif()

install(FILES ${EMsoftLib_SOURCE_DIR}/EMsoftLib.h
  DESTINATION include/EMsoftLib
  COMPONENT Applications
  )

if(APPLE AND BUILD_SHARED_LIBS)
  set(QAB_INSTALL_DEST "lib")
  set(osx_app_name "libEMsoftLib.dylib")
  get_property(EMsoftBinaryDir GLOBAL PROPERTY EMsoftBinaryDir)
  set(OSX_MAKE_STANDALONE_BUNDLE_CMAKE_SCRIPT "${EMsoft_BINARY_DIR}/EMsoftLib/EMsoftLib_CompleteTool.cmake")
  set(OPTIMIZE_BUNDLE_SHELL_SCRIPT            "${EMsoft_BINARY_DIR}/EMsoftLib/EMsoftLib_OptimizeTool.sh")
  set(PROJECT_INSTALL_DIR "lib")
  configure_file("${CMP_OSX_TOOLS_SOURCE_DIR}/CompleteTool.cmake.in"
                "${OSX_MAKE_STANDALONE_BUNDLE_CMAKE_SCRIPT}" @ONLY IMMEDIATE)
  configure_file("${CMP_OSX_TOOLS_SOURCE_DIR}/CompleteTool.sh.in"
                "${OPTIMIZE_BUNDLE_SHELL_SCRIPT}" @ONLY IMMEDIATE)
  #install(SCRIPT "${OSX_MAKE_STANDALONE_BUNDLE_CMAKE_SCRIPT}" COMPONENT ${QAB_COMPONENT})
endif()


# --------------------------------------------------------------------
include(CMakePackageConfigHelpers)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/EMsoft/EMsoftLibTargetsConfigVersion.cmake"
  VERSION ${EMsoft_VERSION}
  COMPATIBILITY AnyNewerVersion
)
#if(BUILD_SHARED_LIBS)
  export(EXPORT EMsoftLibTargets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/EMsoft/EMsoftLibTargets.cmake"
    NAMESPACE EMsoft::
  )
#endif()

configure_file(cmake/EMsoftConfig.cmake
  "${CMAKE_CURRENT_BINARY_DIR}/EMsoft/EMsoftConfig.cmake"
  @ONLY
)

set(ConfigPackageLocation lib/cmake/EMsoft)

#if(BUILD_SHARED_LIBS)
  install(EXPORT EMsoftLibTargets
    FILE
      EMsoftLibTargets.cmake
    NAMESPACE
      EMsoft::
    DESTINATION
      ${ConfigPackageLocation}
  )

#endif()

install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/EMsoft/EMsoftConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/EMsoft/EMsoftLibTargetsConfigVersion.cmake"
  DESTINATION
    ${ConfigPackageLocation}
  COMPONENT
    Devel
)





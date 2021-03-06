

include("${EMsoft_SOURCE_DIR}/Source/EMsoft_Functions.cmake")

set(EXE_LINK_LIBRARIES "")

if (Fortran_COMPILER_NAME MATCHES "gfortran.*")
  set(FORTRAN_LIBRARIES gomp gcc_eh)
endif()

if (Fortran_COMPILER_NAME MATCHES "ifort.*")
  if(WIN32)
    add_definitions("/Qopenmp /Qdiag-disable:11082 /Qip")
  endif()
endif()


include_directories("${EMsoftHDFLib_BINARY_DIR}")
set(LIB_SEARCH_DIRS ${CMAKE_LIBRARY_OUTPUT_DIRECTORY} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})

set(APP_DIR "${EMsoft_SOURCE_DIR}/Source/SEM")
set(TMPLT_DIR "${EMsoft_SOURCE_DIR}/NamelistTemplates")


if(EMsoft_ENABLE_HDF5_SUPPORT)
  
  string(TOLOWER ${LIB_TYPE} lib_type)
  set(hdf5LinkLibs hdf5 hdf5_f90cstub hdf5_fortran hdf5_hl hdf5_hl_f90cstub hdf5_hl_fortran)
  set(comps ${hdf5LinkLibs})
  if (HDF5_VERSION_STRING VERSION_GREATER 1.8.15)
    set(hdf5LinkLibs "")
    foreach(comp ${comps})
      if(TARGET ${comp}-${lib_type})
        set(hdf5LinkLibs ${hdf5LinkLibs} ${comp}-${lib_type})
      endif()
    endforeach()
  endif()

  set(EXE_LINK_LIBRARIES ${EXE_LINK_LIBRARIES} ${hdf5LinkLibs} EMsoftHDFLib)
  
  Add_EMsoft_Executable(TARGET EMEBSD 
                        SOURCES ${APP_DIR}/EMEBSD.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMEBSD.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMkinematical
                        SOURCES ${APP_DIR}/EMkinematical.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMkinematical.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMEBSDmaster 
                        SOURCES ${APP_DIR}/EMEBSDmaster.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMEBSDmaster.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMEBSDFull
                        SOURCES ${APP_DIR}/EMEBSDFull.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMEBSDFull.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMECP 
                        SOURCES ${APP_DIR}/EMECP.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMECP.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMECPmaster 
                        SOURCES ${APP_DIR}/EMECPmaster.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMECPmaster.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMKosselmaster 
                        SOURCES ${APP_DIR}/EMKosselmaster.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMKosselmaster.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMMC 
                        SOURCES ${APP_DIR}/EMMC.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMMC.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMECCI 
                        SOURCES ${APP_DIR}/EMECCI.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMECCI.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMSoft_Executable(TARGET EMMCOpenCL 
                        SOURCES ${APP_DIR}/EMMCOpenCL.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMMCOpenCL.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)
  
  Add_EMsoft_Executable(TARGET EMECPSingle 
                        SOURCES ${APP_DIR}/EMECPSingle.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMECPSingle.template 
                        SOLUTION_FOLDER SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMKAM
                        SOURCES ${APP_DIR}/EMKAM.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMKAM.template 
                        SOLUTION_FOLDER EMsoftPublic/SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMAverageOrient
                        SOURCES ${APP_DIR}/EMAverageOrient.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMAverageOrient.template 
                        SOLUTION_FOLDER EMsoftPublic/SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMOrientationSimilarity
                        SOURCES ${APP_DIR}/EMOrientationSimilarity.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMOrientationSimilarity.template 
                        SOLUTION_FOLDER EMsoftPublic/SEM 
                        INSTALL_PROGRAM TRUE)

  Add_EMsoft_Executable(TARGET EMOrientationViz
                        SOURCES ${APP_DIR}/EMOrientationViz.f90 
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES} 
                        TEMPLATE ${TMPLT_DIR}/EMOrientationViz.template 
                        SOLUTION_FOLDER EMsoftPublic/SEM 
                        INSTALL_PROGRAM TRUE)

endif()


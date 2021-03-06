

#configure_file("${EMsoft_SOURCE_DIR}/Source/Test/TestFileLocations.f90.in"
#  "${EMsoft_BINARY_DIR}/EMsoftPublicTestFileLocations.f90")


include("${EMsoft_SOURCE_DIR}/Source/EMsoft_Functions.cmake")
set(APP_DIR "${EMsoft_SOURCE_DIR}/Source/Test")
set(TMPLT_DIR "${EMsoft_SOURCE_DIR}/NamelistTemplates")

set(EMsoftTestDir "${EMsoft_SOURCE_DIR}/Source/Test")
include_directories("${EMsoftTestDir}")
include_directories("${EMsoft_BINARY_DIR}")
include_directories("${EMsoftHDFLib_BINARY_DIR}")


#------------------------------------------------------------------------------
# in order to add a new unit test the following needs to be performed:
# For a Unit Test called "foo":
# Create a fooTest.f90 file that implements the following function:
# module foo
#
# contains
#  subroutine fooExecuteTest(result) &
#  bind(c, name='fooExecuteTest') ! this routine is callable from a C/C++ program
#  !DEC$ ATTRIBUTES DLLEXPORT :: fooExecuteTest
#
# end subroutine fooExecuteTest
#
# end module foo
#
# Then add the proper cmake code below to instantiate the test:
# AddEMsoftUnitTest(TARGET fooTest
#                   SOURCES ${EMsoftTestDir}/fooTest.f90
#                   TEST_NAME fooTest
#                   LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
#                   SOLUTION_FOLDER EMsoftPublic/Test)
#
# The argument LINK_LIBRARIES needs to have all the proper libraries that the
# test will link against. EXE_LINK_LIBRARIES is just a CMake variable that
# the EMsoft project uses internally to list the most common 3rd part and
# internal libraries that are used. Your test may have additional libraries
# such as JSON Fortran or BLAS/LAPACK for example that are also needed.

#  /NODEFAULTLIB:msvcrt.lib /NODEFAULTLIB:msvcmrt.lib /NODEFAULTLIB:msvcurt.lib /NODEFAULTLIB:msvcrtd.lib

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

      AddEMsoftUnitTest(TARGET HDFtextfileTest
                        SOURCES ${EMsoftTestDir}/HDFtextfileTest.f90
                        TEST_NAME HDFtextfile
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFintegerTest
                        SOURCES ${EMsoftTestDir}/HDFintegerTest.f90
                        TEST_NAME HDFinteger
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFcharTest
                        SOURCES ${EMsoftTestDir}/HDFcharTest.f90
                        TEST_NAME HDFchar
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFfloatTest
                        SOURCES ${EMsoftTestDir}/HDFfloatTest.f90
                        TEST_NAME HDFfloat
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFdoubleTest
                        SOURCES ${EMsoftTestDir}/HDFdoubleTest.f90
                        TEST_NAME HDFdouble
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFhyperslabTest
                        SOURCES ${EMsoftTestDir}/HDFhyperslabTest.f90
                        TEST_NAME HDFhyperslab
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET HDFstackTest
                        SOURCES ${EMsoftTestDir}/HDFstackTest.f90
                        TEST_NAME HDFstack
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET MODcrystalTest
                        SOURCES ${EMsoftTestDir}/MODcrystalTest.f90
                        TEST_NAME MODcrystal
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)

      AddEMsoftUnitTest(TARGET MODMuellerCalculusTest
                        SOURCES ${EMsoftTestDir}/MODMuellerCalculusTest.f90
                        TEST_NAME MODMuellerCalculus
                        LINK_LIBRARIES ${EXE_LINK_LIBRARIES}
                        SOLUTION_FOLDER EMsoftPublic/Test)



endif()

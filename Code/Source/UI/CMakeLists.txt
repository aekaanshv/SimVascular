
SET(CXXSRCS main.cxx SimVascular_Init.cxx)

UNSET(OPTLIBS)

if(NOT SV_BUILD_SHARED_LIBS)
	SET(CXXSRCS ${CXXSRCS}
		 ${SV_SOURCE_DIR}/Source/Common/Geometry/cv_geom_init.cxx
                 ${SV_SOURCE_DIR}/Source/PostProcessing/cv_post_init.cxx
                 ${SV_SOURCE_DIR}/Source/ImageProcessing/cv_image_init.cxx
                 ${SV_SOURCE_DIR}/Source/Common/Repository/cv_repos_init.cxx
                 ${SV_SOURCE_DIR}/Source/Model/SolidModel/cv_solid_init.cxx
                 ${SV_SOURCE_DIR}/Source/Legacy/LevelSet/cv_LsetCore_init.cxx
                 ${SV_SOURCE_DIR}/Source/Legacy/LevelSet/cv_LsetV_init.cxx
		 ${SV_SOURCE_DIR}/Source/Legacy/LevelSet/cv_Lset_init.cxx
                 ${SV_SOURCE_DIR}/Source/Common/Utils/cv_get_tcl_interp_init.cxx
                 ${SV_SOURCE_DIR}/Source/Mesh/MeshObject/cv_mesh_init.cxx
                 ${SV_SOURCE_DIR}/Source/Common/Utils/cv_math_init.cxx
		 ${SV_SOURCE_DIR}/Source/Model/PolyDataSolidModel/cv_polydatasolid_init.cxx)
endif()

if(NOT ${USE_SYSTEM_TCL})
	include_directories(${TCL_INCLUDE_PATH})
	include_directories(${TK_INCLUDE_PATH})
endif()

if (SV_USE_PYTHON)
	include_directories(${PYTHON_INCLUDE_PATH})
endif()

if(SV_USE_TETGEN)
	include_directories(${TETGEN_INCLUDE_DIR})
	SET(CXXSRCS ${CXXSRCS}
	${SV_SOURCE_DIR}/Source/Mesh/TetGenMeshObject/cv_tetgen_mesh_init.cxx)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_tetgen_mesh)
endif()

if(SV_USE_MMG)
	include_directories(${MMG_INCLUDE_DIR})
	SET(CXXSRCS ${CXXSRCS}
	${SV_SOURCE_DIR}/Source/Mesh/MMGMeshUtils/cv_mmg_mesh_init.cxx)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_mmg_mesh)
endif()

if(SV_USE_MESHSIM)
	if (NOT SV_USE_MESHSIM_SHARED_LIBRARIES)
	  SET(CXXSRCS ${CXXSRCS}
	  ${SV_SOURCE_DIR}/Source/Mesh/MeshSimMeshObject/cv_meshsim_mesh_init.cxx)
	  SET(OPTLIBS ${OPTLIBS} lib_simvascular_meshsim_mesh)
        endif()
endif()

if(SV_USE_MESHSIM_DISCRETE_MODEL)
	if (NOT SV_USE_MESHSIM_DISCRETE_SHARED_LIBRARIES)
	  SET(CXXSRCS ${CXXSRCS}
	  ${SV_SOURCE_DIR}/Source/Model/MeshSimDiscreteSolidModel/cv_discrete_init.cxx)
	  SET(OPTLIBS ${OPTLIBS} lib_simvascular_discrete)
        endif()
endif()

if(SV_USE_PARASOLID)
	if (NOT SV_USE_PARASOLID_SHARED_LIBRARIES)
	  SET(CXXSRCS ${CXXSRCS}
	  ${SV_SOURCE_DIR}/Licensed/ParasolidSolidModel/cv_parasolid_solid_init.cxx)
	  SET(OPTLIBS ${OPTLIBS} lib_simvascular_parasolid)
        endif()
endif()

if(SV_USE_OpenCASCADE)
	SET(CXXSRCS ${CXXSRCS}
	${SV_SOURCE_DIR}/Source/Model/OCCTSolidModel/cv_occt_init.cxx)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_opencascade)
        link_directories(${FREETYPE_DIR}/lib)
endif()

if(SV_USE_ITK)
	include_directories(${SV_SOURCE_DIR}/Source/Segmentation/ITK/Include)
	include_directories(${SV_SOURCE_DIR}/Source/Segmentation/ITK/Util)
	SET(CXXSRCS ${CXXSRCS} ${SV_SOURCE_DIR}/Source/Segmentation/ITK/cv_ITKLset2d_init.cxx
			       ${SV_SOURCE_DIR}/Source/Segmentation/ITK/cv_ITKLset3d_init.cxx
			       ${SV_SOURCE_DIR}/Source/Segmentation/ITK/cv_ITKUtils_init.cxx
			       ${SV_SOURCE_DIR}/Source/Segmentation/ITK/cv_ITKLset_init.cxx)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_itklset)
  endif()
	
if(SV_USE_TET_ADAPTOR)
	SET(CXXSRCS ${CXXSRCS}
	${SV_SOURCE_DIR}/Source/Mesh/AdaptObject/cv_adapt_init.cxx
	${SV_SOURCE_DIR}/Source/Mesh/TetGenAdapt/cv_tetgen_adapt_init.cxx)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_adaptor)
	SET(OPTLIBS ${OPTLIBS} lib_simvascular_tet_adaptor)
endif()

if (SV_USE_MESHSIM_ADAPTOR)
	if(NOT SV_USE_MESHSIM_SHARED_LIBRARIES)
	  set(CXXSRCS ${CXXSRCS}
	  ${SV_SOURCE_DIR}/Source/Mesh/AdaptObject/cv_adapt_init.cxx
	  ${SV_SOURCE_DIR}/Source/Mesh/MeshSimAdapt/cv_meshsim_adapt_init.cxx)
	  SET(OPTLIBS ${OPTLIBS} lib_simvascular_adaptor)
	  SET(OPTLIBS ${OPTLIBS} lib_simvascular_meshsim_adaptor)
        endif()
endif()

simvascular_add_executable(${SV_EXE}
	SRCS ${CXXSRCS}
	DEV_SCRIPT_NAME "mysim"
	INSTALL_SCRIPT_NAME "simvascular"
	INSTALL_COMPONENT CoreExecutables
	INSTALL_DESTINATION ${SV_INSTALL_RUNTIME_DIR})
#message("OPTLIBS ${OPTLIBS}")

target_link_libraries(${SV_EXE}
			lib_simvascular_post lib_simvascular_image
			lib_simvascular_repository lib_simvascular_lset
			lib_simvascular_solid lib_simvascular_mesh
			lib_simvascular_sysgeom lib_simvascular_utils
			lib_simvascular_globals lib_simvascular_polydatasolid)

if(SV_USE_PYTHON)
target_link_libraries(${SV_EXE}
                        lib_simvascular_tclpython)
endif()

target_link_libraries(${SV_EXE} ${OPTLIBS}
			${VTK_LIBRARIES} ${TCL_LIBRARY} ${TK_LIBRARY}
			${PARASOLID_LIBRARIES} ${MESHSIM_LIBRARIES}
			${OpenCASCADE_LIBRARIES}
			${ZLIB_LIBRARY} ${ITK_LIBRARIES}
			${INTELRUNTIME_LIBRARIES}
			${GLOBAL_LIBRARIES}
			${MMG_LIBRARY}
			${FREETYPE_LIBRARY})

if(SV_USE_PYTHON)
  target_link_libraries(${SV_EXE} ${PYTHON_LIBRARY})
endif()

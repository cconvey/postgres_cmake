include_directories("${PROJECT_SOURCE_DIR}/src/include")

set(extension_name pg_trgm)

add_library(${extension_name} MODULE
	trgm_op.c
	trgm_gist.c
	trgm_gin.c
	trgm_regexp.c
)
target_link_libraries(${extension_name} ${contrib_libs})
set_target_properties(${extension_name} PROPERTIES PREFIX "")
add_dependencies(${extension_name} postgres)
if(MSVC)
	gen_def(${extension_name})
endif()
if(NOT PGXS)
	CMAKE_SET_TARGET_FOLDER(${extension_name} contrib)
endif()
install(TARGETS ${extension_name}
		RUNTIME DESTINATION ${PGBINDIR}
		LIBRARY DESTINATION ${LIBDIR})
install(FILES
	${extension_name}.control
	${extension_name}--1.3.sql
	${extension_name}--1.2--1.3.sql
	${extension_name}--1.1--1.2.sql
	${extension_name}--1.0--1.1.sql
	${extension_name}--unpackaged--1.0.sql
	DESTINATION ${PGSHAREDIR}/extension)

set(regress_files pg_trgm pg_word_trgm)
CONTRIB_REGRESS_CHECK(${extension_name} "" "${regress_files}")

include_directories("${PROJECT_SOURCE_DIR}/src/include")

set(extension_name hstore)

add_library(${extension_name} SHARED
	hstore_io.c
	hstore_op.c
	hstore_gist.c
	hstore_gin.c
	hstore_compat.c
)
target_link_libraries(${extension_name} ${contrib_libs})
set_target_properties(${extension_name} PROPERTIES PREFIX "")
add_dependencies(${extension_name} postgres)
install(TARGETS ${extension_name}
		RUNTIME DESTINATION ${PGBINDIR}
		LIBRARY DESTINATION ${LIBDIR})
install(FILES
	${extension_name}.control
	${extension_name}--1.3.sql
	${extension_name}--1.0--1.1.sql
	${extension_name}--1.1--1.2.sql
	${extension_name}--1.2--1.3.sql
	${extension_name}--unpackaged--1.0.sql
	DESTINATION ${PGSHAREDIR}/extension)

set(regress_files hstore)
CONTRIB_REGRESS_CHECK(${extension_name} "" "${regress_files}")
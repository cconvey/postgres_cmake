include_directories(BEFORE
	"${PROJECT_SOURCE_DIR}/src/include/libpq"
	"${PROJECT_SOURCE_DIR}/src/interfaces/libpq"
	"${PROJECT_SOURCE_DIR}/src/bin/pg_dump"
)

add_executable(pg_ctl
	pg_ctl.c
)

target_link_libraries(pg_ctl
	port
	pq
	pgcommon
)

CMAKE_SET_TARGET_FOLDER(pg_ctl bin)

install(TARGETS pg_ctl
		RUNTIME DESTINATION ${PGBINDIR}
		LIBRARY DESTINATION ${LIBDIR})

if(ENABLE_NLS)
	MAKE_MO(pg_ctl "cs;de;es;fr;it;ja;ko;pl;pt_BR;ru;sv;zh_CN")
endif()

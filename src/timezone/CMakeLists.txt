#TODO compile timezones

include_directories(
	"${PROJECT_SOURCE_DIR}/src/include/libpq"
	"${PROJECT_SOURCE_DIR}/src/interfaces/libpq"
	"${PROJECT_SOURCE_DIR}/src/bin/pg_dump"
)



set(tzdata 
	${CMAKE_CURRENT_SOURCE_DIR}/data/africa
	${CMAKE_CURRENT_SOURCE_DIR}/data/antarctica
	${CMAKE_CURRENT_SOURCE_DIR}/data/asia
	${CMAKE_CURRENT_SOURCE_DIR}/data/australasia
	${CMAKE_CURRENT_SOURCE_DIR}/data/europe
	${CMAKE_CURRENT_SOURCE_DIR}/data/northamerica
	${CMAKE_CURRENT_SOURCE_DIR}/data/southamerica
	${CMAKE_CURRENT_SOURCE_DIR}/data/pacificnew
	${CMAKE_CURRENT_SOURCE_DIR}/data/etcetera
	${CMAKE_CURRENT_SOURCE_DIR}/data/factory
	${CMAKE_CURRENT_SOURCE_DIR}/data/backward
	${CMAKE_CURRENT_SOURCE_DIR}/data/systemv
)

add_executable(zic
	zic.c
)
target_link_libraries(zic
	port
)

if(MSVC)
	foreach( OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES} )
		string( TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG )
		set_target_properties(zic PROPERTIES RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_CURRENT_BINARY_DIR} )
		set_target_properties(zic PROPERTIES LIBRARY_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_CURRENT_BINARY_DIR} )
		set_target_properties(zic PROPERTIES ARCHIVE_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_CURRENT_BINARY_DIR} )
	endforeach( OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES )
endif(MSVC)


install(FILES 
	tznames/Africa.txt
	tznames/America.txt
	tznames/Antarctica.txt
	tznames/Asia.txt
	tznames/Atlantic.txt
	tznames/Australia.txt
	tznames/Etc.txt
	tznames/Europe.txt
	tznames/Indian.txt
	tznames/Pacific.txt
	tznames/Default
	tznames/Australia
	tznames/India
DESTINATION ${PGSHAREDIR}/timezonesets)

function(JOIN VALUES GLUE OUTPUT)
  string (REGEX REPLACE "([^\\]|^);" "\\1${GLUE}" _TMP_STR "${VALUES}")
  string (REGEX REPLACE "[\\](.)" "\\1" _TMP_STR "${_TMP_STR}") #fixes escaping
  set (${OUTPUT} "${_TMP_STR}" PARENT_SCOPE)
endfunction()

JOIN("${tzdata}" " " tzdata_string)
install(
	CODE "message('zic \$ENV{DESTDIR}${PGSHAREDIR}/timezone')"
	CODE "execute_process(COMMAND ${CMAKE_CURRENT_BINARY_DIR}/zic${CMAKE_EXECUTABLE_SUFFIX} -d \"\$ENV{DESTDIR}${PGSHAREDIR}/timezone\" -p \"US/Eastern\" ${tzdata_string})"
)


# ADD_SUBDIRECTORY(bash-completion)

INCLUDE_DIRECTORIES(
  ../../include
)

MACRO (ADD_MDB_UTIL UTIL UTILSRC INSTALLUTIL)
  IF(FAKE_GLIB)
    SET(UTILSRC
      ${UTILSRC}
      ../mdbtools/fakeglib.c
      )
  ELSE(FAKE_GLIB)
    INCLUDE_DIRECTORIES(SYSTEM ${GLIB_INCLUDE_DIRS})
  ENDIF(FAKE_GLIB)

  ADD_EXECUTABLE (${UTIL} ${UTILSRC})

  TARGET_LINK_LIBRARIES(${UTIL} libmdb)
  IF(NOT FAKE_GLIB)
    TARGET_LINK_LIBRARIES(${UTIL} ${GLIB_LDFLAGS})
  ENDIF(NOT FAKE_GLIB)

  # install
  IF (INSTALLUTIL)
    INSTALL (TARGETS ${UTIL}
      BUNDLE DESTINATION ${MDBTOOLS_BIN_DIR}
      RUNTIME DESTINATION ${MDBTOOLS_BIN_DIR}
    )
  ENDIF(INSTALLUTIL)

ENDMACRO (ADD_MDB_UTIL)

#installed utilities
ADD_MDB_UTIL(mdb-tables mdb-tables.c TRUE)
ADD_MDB_UTIL(mdb-array mdb-array.c TRUE)
ADD_MDB_UTIL(mdb-schema mdb-schema.c TRUE)
ADD_MDB_UTIL(mdb-parsecsv mdb-parsecsv.c TRUE)
ADD_MDB_UTIL(mdb-header mdb-header.c TRUE)
#ADD_MDB_UTIL(mdb-ver mdb-ver.c TRUE)
ADD_MDB_UTIL(mdb-prop mdb-prop.c TRUE)
ADD_MDB_UTIL(mdb-count mdb-count.c TRUE)

IF(WITH_SQL)
  ADD_MDB_UTIL(mdb-sql mdb-sql.c TRUE)
  TARGET_LINK_LIBRARIES(mdb-sql libmdbsql)
ENDIF(WITH_SQL)

# utilites which aren't for installation (debugging utils)
ADD_MDB_UTIL(mdb-import mdb-import.c FALSE)
ADD_MDB_UTIL(prtable prtable.c FALSE)
ADD_MDB_UTIL(prcat prcat.c FALSE)
ADD_MDB_UTIL(prdata prdata.c FALSE)
ADD_MDB_UTIL(prkkd prkkd.c FALSE)
ADD_MDB_UTIL(prdump prdump.c FALSE)
ADD_MDB_UTIL(prole prole.c FALSE)
ADD_MDB_UTIL(updrow updrow.c FALSE)
ADD_MDB_UTIL(prindex prindex.c FALSE)

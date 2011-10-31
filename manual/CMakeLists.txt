## Process this file with cmake
#====================================================================
#  NeXus - Neutron & X-ray Common Data Format
#  
#  CMakeLists for building the NeXus library and applications.
#
#  Copyright (C) 2011 Stephen Rankin
#  
#  This library is free software; you can redistribute it and/or
#  modify it under the terms of the GNU Lesser General Public
#  License as published by the Free Software Foundation; either
#  version 2 of the License, or (at your option) any later version.
# 
#  This library is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
#  Lesser General Public License for more details.
# 
#  You should have received a copy of the GNU Lesser General Public
#  License along with this library; if not, write to the Free 
#  Software Foundation, Inc., 59 Temple Place, Suite 330, Boston, 
#  MA  02111-1307  USA
#             
#  For further information, see <http://www.nexusformat.org>
#
#
#====================================================================

find_program(DBLATEX dblatex)
find_program(XSLTPROC xsltproc)

set(XSLT_PATH ${CMAKE_SOURCE_DIR}/xslt)
set (XSLT ${XSLT_PATH}/nxdl2docbook.xsl)
set (DBXSL ${XSLT_PATH}/docbook-xsl-1.75.2)

set(DYNAMIC_DOCBOOK_SECTIONS classes/BaseClassSections.xml classes/ApplicationClassSections.xml 
                             classes/ContributedClassSections.xml types.xml units.xml nxdl_desc.xml
                             subtitle.xml)

set (CLASS_HEAD_PYTHON ${CMAKE_SOURCE_DIR}/utils/classSectionHead.py)

set(NXDL_SUFFIX nxdl.xml)

# relative locations for the NXDL instance files
set (BASE_CLASS_PATH ${CMAKE_SOURCE_DIR}/base_classes)
set (APPLICATIONS_CLASS_PATH ${CMAKE_SOURCE_DIR}/applications)
set (CONTRIBUTED_CLASS_PATH ${CMAKE_SOURCE_DIR}/contributed_definitions)

set (VPATH ${BASE_CLASS_PATH} ${APPLICATIONS_CLASS_PATH} ${CONTRIBUTED_CLASS_PATH})

# make lists of all the NXDL instances 
# (useful to make separate lists for category)
file(GLOB NXDL_BASE_CLASSES RELATIVE ${BASE_CLASS_PATH} ${BASE_CLASS_PATH}/*.${NXDL_SUFFIX})
file(GLOB NXDL_APPLICATIONS RELATIVE ${APPLICATIONS_CLASS_PATH} ${APPLICATIONS_CLASS_PATH}/*.${NXDL_SUFFIX})
file(GLOB NXDL_CONTRIBUTED RELATIVE ${CONTRIBUTED_CLASS_PATH} ${CONTRIBUTED_CLASS_PATH}/*.${NXDL_SUFFIX})

# put them all together in one big list
# be careful, empty directories will return the $(DIR)/*.$(NXDL_SUFFIX) search pattern
set (ALL_NXDL ${NXDL_BASE_CLASSES} ${NXDL_APPLICATIONS} ${NXDL_CONTRIBUTED})

set(BUILT_NEXUSMANUAL NeXusManual.pdf NeXusManual.html NeXusRefDoc.pdf)

set(CLEANING_LIST ${BUILT_NEXUSMANUAL} ${BUILT_NEXUSREFDOC} html/ classes/ types.xml units.xml nxdl_desc.xml)

# for details, see: http://dblatex.sourceforge.net/doc/sec-custom.html
set (DBLATEX_OPTS -P table.in.float=0 -P doc.lot.show="figure" -b pdftex -tpdf)

set(OUTPUT_BASE_TEXT_FILES)
set(OUTPUT_BASE_XML_FILES)
set(OUTPUT_BASE_XML_TARGETS)
set(TEXT_FILE)
set(XML_FILE)
foreach(NXDL_FILE ${NXDL_BASE_CLASSES})
  string(REPLACE "${NXDL_SUFFIX}" "txt" TEXT_FILE ${NXDL_FILE})
  string(REPLACE "${NXDL_SUFFIX}" "xml" XML_FILE ${NXDL_FILE})
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} ${BASE_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${BASE_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating BASE TXT File ${TEXT_FILE}"
  ) 
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} ${XSLT} ${BASE_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${BASE_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating BASE XML File ${XML_FILE}"
  ) 
  set(OUTPUT_BASE_TEXT_FILES ${OUTPUT_BASE_TEXT_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE})
  set(OUTPUT_BASE_XML_FILES ${OUTPUT_BASE_XML_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE})
  set(OUTPUT_BASE_XML_TARGETS ${OUTPUT_BASE_XML_TARGETS} classes/${XML_FILE})
endforeach(NXDL_FILE)

add_custom_target(BuildBASETXTFiles ALL DEPENDS ${OUTPUT_BASE_TEXT_FILES})
add_custom_target(BuildBASEXMLFiles ALL DEPENDS ${OUTPUT_BASE_XML_FILES})

set(OUTPUT_APPLICATIONS_TEXT_FILES)
set(OUTPUT_APPLICATIONS_XML_FILES)
set(OUTPUT_APPLICATIONS_XML_TARGETS)
set(TEXT_FILE)
set(XML_FILE)
foreach(NXDL_FILE ${NXDL_APPLICATIONS})
  string(REPLACE "${NXDL_SUFFIX}" "txt" TEXT_FILE ${NXDL_FILE})
  string(REPLACE "${NXDL_SUFFIX}" "xml" XML_FILE ${NXDL_FILE})
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} ${APPLICATIONS_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${APPLICATIONS_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating APPLICATIONS TXT File ${TEXT_FILE}"
  ) 
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} ${XSLT} ${APPLICATIONS_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${APPLICATIONS_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating APPLICATIONS XML File ${XML_FILE}"
  ) 
  set(OUTPUT_APPLICATIONS_TEXT_FILES ${OUTPUT_APPLICATIONS_TEXT_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE})
  set(OUTPUT_APPLICATIONS_XML_FILES ${OUTPUT_APPLICATIONS_XML_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE})
  set(OUTPUT_APPLICATIONS_XML_TARGETS ${OUTPUT_APPLICATIONS_XML_TARGETS} classes/${XML_FILE})
endforeach(NXDL_FILE)

add_custom_target(BuildAPPLICATIONSTXTFiles ALL DEPENDS ${OUTPUT_APPLICATIONS_TEXT_FILES})
add_custom_target(BuildAPPLICATIONSXMLFiles ALL DEPENDS ${OUTPUT_APPLICATIONS_XML_FILES})

set(OUTPUT_CONTRIBUTED_TEXT_FILES)
set(OUTPUT_CONTRIBUTED_XML_FILES)
set(OUTPUT_CONTRIBUTED_XML_TARGETS)
set(TEXT_FILE)
set(XML_FILE)
foreach(NXDL_FILE ${NXDL_CONTRIBUTED})
  string(REPLACE "${NXDL_SUFFIX}" "txt" TEXT_FILE ${NXDL_FILE})
  string(REPLACE "${NXDL_SUFFIX}" "xml" XML_FILE ${NXDL_FILE})
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE} ${CONTRIBUTED_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${CONTRIBUTED_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating CONTRIBUTED TXT File ${TEXT_FILE}"
  ) 
  add_custom_command(
    OUTPUT ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} 
    COMMAND ${XSLTPROC}
    ARGS -o ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE} ${XSLT} ${CONTRIBUTED_CLASS_PATH}/${NXDL_FILE}
    DEPENDS ${CONTRIBUTED_CLASS_PATH}/${NXDL_FILE}
    COMMENT   "Generating CONTRIBUTED XML File ${XML_FILE}"
  ) 
  set(OUTPUT_CONTRIBUTED_TEXT_FILES ${OUTPUT_CONTRIBUTED_TEXT_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${TEXT_FILE})
  set(OUTPUT_CONTRIBUTED_XML_FILES ${OUTPUT_CONTRIBUTED_XML_FILES} ${CMAKE_SOURCE_DIR}/manual/classes/${XML_FILE})
  set(OUTPUT_CONTRIBUTED_XML_TARGETS ${OUTPUT_CONTRIBUTED_XML_TARGETS} classes/${XML_FILE})
endforeach(NXDL_FILE)

add_custom_target(BuildCONTRIBUTEDTXTFiles ALL DEPENDS ${OUTPUT_CONTRIBUTED_TEXT_FILES})
add_custom_target(BuildCONTRIBUTEDXMLFiles ALL DEPENDS ${OUTPUT_CONTRIBUTED_XML_FILES})

add_custom_command( 
    OUTPUT    ${CMAKE_SOURCE_DIR}/manual/subtitle.xml
    COMMAND   ${CMAKE_COMMAND}
    ARGS      -P ${CMAKE_SOURCE_DIR}/utils/setsubtitle.cmake
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/manual
    COMMENT   "Generating subtitle.xml"
)

add_custom_target(NexusCreateSubtitleBuild ALL echo
    DEPENDS   ${CMAKE_SOURCE_DIR}/manual/subtitle.xml
)

if (XSLTPROC)

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/types.xml
        COMMAND   ${XSLTPROC}
        ARGS      ${XSLT_PATH}/nxdl_types2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdlTypes.xsd > ${CMAKE_SOURCE_DIR}/manual/types.xml
        DEPENDS   ${XSLT_PATH}/nxdl_types2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdlTypes.xsd
        COMMENT   "Generating types.xml"
    )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/units.xml
        COMMAND   ${XSLTPROC}
        ARGS      ${XSLT_PATH}/nxdl_units2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdlTypes.xsd > ${CMAKE_SOURCE_DIR}/manual/units.xml
        DEPENDS   ${XSLT_PATH}/nxdl_units2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdlTypes.xsd ${CMAKE_SOURCE_DIR}/manual/types.xml
        COMMENT   "Generating units.xml"
    )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/nxdl_desc.xml
        COMMAND   ${XSLTPROC}
        ARGS      ${XSLT_PATH}/nxdl_desc2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdl.xsd > ${CMAKE_SOURCE_DIR}/manual/nxdl_desc.xml
        DEPENDS   ${XSLT_PATH}/nxdl_desc2docbook.xsl ${CMAKE_SOURCE_DIR}/nxdl.xsd ${CMAKE_SOURCE_DIR}/manual/units.xml
        COMMENT   "Generating nxdl_desc.xml"
    )

    add_custom_target(NexusGeneratingXML ALL echo
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/nxdl_desc.xml
    )
  
    add_dependencies(NexusGeneratingXML NexusCreateSubtitleBuild)

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/index.html
        COMMAND   ${XSLTPROC}
        ARGS      --xinclude --stringparam use.extensions 0 --stringparam callout.graphics.path "img/callouts/" --stringparam chunker.output.encoding UTF-8 ${DBXSL}/html/onechunk.xsl ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/manual
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        COMMENT   "Generating ${CMAKE_SOURCE_DIR}/manual/index.html"
    )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/NeXusManual.html
        COMMAND   ${CMAKE_COMMAND}
        ARGS      -E rename ${CMAKE_SOURCE_DIR}/manual/index.html ${CMAKE_SOURCE_DIR}/manual/NeXusManual.html
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/index.html
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/manual
        COMMENT   "Generating NeXusManual.html"
    )

    add_custom_target(NexusHTMLManual2 ALL echo
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/NeXusManual.html
    )

#    if(NOT EXISTS ${CMAKE_SOURCE_DIR}/manual/html)

        add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/html
        COMMAND   ${CMAKE_COMMAND}
        ARGS      -E make_directory ${CMAKE_SOURCE_DIR}/manual/html
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        COMMENT   "Generating ${CMAKE_SOURCE_DIR}/manual/html"
       )

#    endif(NOT EXISTS ${CMAKE_SOURCE_DIR}/manual/html)


    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/html/index.html
        COMMAND   ${XSLTPROC}
        ARGS      --xinclude --stringparam use.id.as.filename 1 --stringparam use.extensions 0 --stringparam callout.graphics.path "../img/callouts/" --stringparam chunker.output.encoding UTF-8 ${DBXSL}/html/chunk.xsl ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/manual/html
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/html
        COMMENT   "Generating ${CMAKE_SOURCE_DIR}/manual/html/index.html"
    )

    add_custom_target(NexusHTMLManual1 ALL echo
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/html/index.html
    )


endif (XSLTPROC)

if (PYTHONINTERP_FOUND)

    file(WRITE ${CMAKE_SOURCE_DIR}/manual/debug.out ${OUTPUT_BASE_XML_TARGETS})

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/classes/BaseClassSections.xml
        COMMAND   ${PYTHON_EXECUTABLE}
        ARGS      ${CLASS_HEAD_PYTHON} 'Base' ${OUTPUT_BASE_XML_TARGETS} > ${CMAKE_SOURCE_DIR}/manual/classes/BaseClassSections.xml
        COMMENT   "Generating BaseClassSections.xml" 
    )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/classes/ApplicationClassSections.xml
        COMMAND   ${PYTHON_EXECUTABLE}
        ARGS      ${CLASS_HEAD_PYTHON} 'Application' ${OUTPUT_APPLICATIONS_XML_TARGETS} > ${CMAKE_SOURCE_DIR}/manual/classes/ApplicationClassSections.xml
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/classes/BaseClassSections.xml
        COMMENT   "Generating ApplicationClassSections.xml"
    )

    add_custom_command( 
        OUTPUT    ContributedClassSections.xml
        COMMAND   ${PYTHON_EXECUTABLE}
        ARGS      ${CLASS_HEAD_PYTHON} 'Contributed' ${OUTPUT_CONTRIBUTED_XML_TARGETS} > ${CMAKE_SOURCE_DIR}/manual/classes/ContributedClassSections.xml
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/classes/ApplicationClassSections.xml
        COMMENT   "Generating ContributedClassSections.xml"
    )

    add_custom_target(NexusClassSections ALL echo
        DEPENDS   ContributedClassSections.xml
    )

    add_dependencies(NexusClassSections BuildBASEXMLFiles BuildAPPLICATIONSXMLFiles BuildCONTRIBUTEDXMLFiles)

endif (PYTHONINTERP_FOUND)

if (DBLATEX)

    set(DBLATEX_OPTS -P table.in.float=0 -P doc.lot.show="figure" -b pdftex -tpdf )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/NeXusManual.pdf
        COMMAND   ${DBLATEX}
        ARGS      -V -Pset.book.num=1 ${DBLATEX_OPTS} ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml -o ${CMAKE_SOURCE_DIR}/manual/NeXusManual.pdf
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        COMMENT   "Generating NeXusManual.pdf"
    )

    add_custom_command( 
        OUTPUT    ${CMAKE_SOURCE_DIR}/manual/NeXusRefDoc.pdf
        COMMAND   ${DBLATEX}
        ARGS      -V -Pset.book.num=2 ${DBLATEX_OPTS} ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml -o ${CMAKE_SOURCE_DIR}/manual/NeXusRefDoc.pdf
        DEPENDS   ${CMAKE_SOURCE_DIR}/manual/NeXusManual.xml
        COMMENT   "Generating NeXusRefDoc.pdf"
    )

    add_custom_target(NeXusManualBuild ALL echo
        DEPENDS   NeXusManual.pdf
    )

    add_custom_target(NeXusRefDocBuild ALL echo
        DEPENDS   NeXusRefDoc.pdf
    )

    add_dependencies(NeXusManualBuild NexusGeneratingXML NexusClassSections NexusCreateSubtitleBuild NexusCreateSubtitleBuild)
    add_dependencies(NeXusRefDocBuild NexusGeneratingXML NexusClassSections NexusCreateSubtitleBuild NexusCreateSubtitleBuild)

endif (DBLATEX)


# Not all CMake Generators support file globbing, so build our tex dependency list manually
# We could get CMake to do the glob, but there's not many files to deal with
set(DOC_LIST
	glscopeclient-manual.tex
	section-copyright.tex
	section-decodes.tex
	section-scope-drivers.tex
	section-powersupply-drivers.tex
	section-exports.tex
	section-gettingstarted.tex
	section-grapheditor.tex
	section-history.tex
	section-internals.tex
	section-legal.tex
	section-mainwindow.tex
	section-protoanalyzer.tex
	section-revision.tex
	section-timeline.tex
	section-transports.tex
	section-triggers.tex
	section-waveformgroups.tex
	section-waveformviews.tex
)
# Save ourselves from having to manually paste this in front of every filename
list(TRANSFORM DOC_LIST PREPEND ${CMAKE_CURRENT_SOURCE_DIR}/)

add_custom_command(
	OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/glscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run1.log 2>&1
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/glscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run2.log 2>&1
	DEPENDS ${DOC_LIST}
	WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
	VERBATIM)

set(NGDOC_LIST
	ngscopeclient-manual.tex
	section-copyright.tex
	section-ng-revision.tex
	section-ng-legal.tex
	section-ng-gettingstarted.tex
	section-ng-mainwindow.tex
	section-ng-waveformgroups.tex
	section-ng-waveformviews.tex
	section-ng-history.tex
	section-ng-grapheditor.tex
	section-transports.tex
	section-bert-drivers.tex
	section-funcgen-drivers.tex
	section-load-drivers.tex
	section-meter-drivers.tex
	section-scope-drivers.tex
	section-powersupply-drivers.tex
	section-rfgen-drivers.tex
	section-triggers.tex
	section-decodes.tex

#	section-internals.tex
#	section-protoanalyzer.tex
#	section-timeline.tex
)
# Save ourselves from having to manually paste this in front of every filename
list(TRANSFORM NGDOC_LIST PREPEND ${CMAKE_CURRENT_SOURCE_DIR}/)

# ngscopeclient manual depends on glscopeclient manual as a hack to avoid parallel building of both
# which can cause race conditions when building the .aux files
add_custom_command(
	OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/ngscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run1.log 2>&1
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/ngscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run2.log 2>&1
	DEPENDS ${NGDOC_LIST} # ${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf
	WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
	VERBATIM)

add_custom_target(doc ALL
	DEPENDS
		#${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf
		${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf)

#install(FILES ${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf
#	DESTINATION doc/glscopeclient)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf
	DESTINATION doc/ngscopeclient)

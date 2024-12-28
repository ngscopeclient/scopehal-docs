# Not all CMake Generators support file globbing, so build our tex dependency list manually
# We could get CMake to do the glob, but there's not many files to deal with
set(NGDOC_LIST
	ngscopeclient-manual.tex
	section-copyright.tex
	section-intro.tex
	section-legal.tex
	section-gettingstarted.tex
	section-mainwindow.tex
	section-dialogs.tex
	section-waveformgroups.tex
	section-waveformviews.tex
	section-history.tex
	section-grapheditor.tex
	section-transports.tex
	section-tutorials.tex
	section-bert-drivers.tex
	section-funcgen-drivers.tex
	section-load-drivers.tex
	section-meter-drivers.tex
	section-misc-drivers.tex
	section-powersupply-drivers.tex
	section-rfgen-drivers.tex
	section-scope-drivers.tex
	section-spectrometer-drivers.tex
	section-streambrowser.tex
	section-triggers.tex
	section-decodes.tex
	section-workspaces.tex

#	section-internals.tex
#	section-protoanalyzer.tex
#	section-timeline.tex
)
# Save ourselves from having to manually paste this in front of every filename
list(TRANSFORM NGDOC_LIST PREPEND ${CMAKE_CURRENT_SOURCE_DIR}/)

add_custom_command(
	OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/ngscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run1.log 2>&1
	COMMAND pdflatex -halt-on-error -interaction batchmode
		--output-directory ${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/ngscopeclient-manual.tex > ${CMAKE_CURRENT_BINARY_DIR}/run2.log 2>&1
	DEPENDS ${NGDOC_LIST}
	WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
	VERBATIM)

# Copy source to build directory
add_custom_command(
	OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/htmlbuild/ngscopeclient-manual.tex

	# Copy entire source directory and subfolders to build directory
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/htmlbuild

	DEPENDS ${NGDOC_LIST}
	VERBATIM)


# Make the HTML output
add_custom_command(
	OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/html/ngscopeclient-manual.html

	# Copy entire source directory and subfolders to build directory
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR}/htmlbuild

	# Get bounding box data for images
	# this is ugly and might not work on non-Linux but is better than nothing for now?
	COMMAND sh -c "ebb -x images/*.png images/filters/*.png ng-images/*.png"

	# Actual building
	COMMAND make4ht -uf html5 --output-dir ${CMAKE_CURRENT_BINARY_DIR}/html
		ngscopeclient-manual.tex "2,sec-filename,fn-in,mathjax" > makehtml.log 2>&1

	DEPENDS ${NGDOC_LIST} ${CMAKE_CURRENT_BINARY_DIR}/htmlbuild/ngscopeclient-manual.tex
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/htmlbuild
	VERBATIM)

add_custom_target(doc ALL
	DEPENDS
		${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf
		${CMAKE_CURRENT_BINARY_DIR}/html/ngscopeclient-manual.html)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/ngscopeclient-manual.pdf
	DESTINATION share/doc/ngscopeclient OPTIONAL)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/html
	DESTINATION share/doc/ngscopeclient OPTIONAL)

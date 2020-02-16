add_custom_command(
    OUTPUT  ${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf
    COMMAND pdflatex -halt-on-error -interaction batchmode ${CMAKE_CURRENT_SOURCE_DIR}/glscopeclient-manual.tex > /dev/null
    COMMAND pdflatex -halt-on-error -interaction batchmode ${CMAKE_CURRENT_SOURCE_DIR}/glscopeclient-manual.tex > /dev/null
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/glscopeclient-manual.tex
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    VERBATIM)
add_custom_target(doc ALL
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/glscopeclient-manual.pdf)

# @@@LICENSE
#
# Copyright (c) 2012 Hewlett-Packard Development Company, L.P.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# LICENSE@@@

cmake_minimum_required(VERSION 2.8.7)

project(rdx-utils-stub NONE)

include(webOS/webOS)
webos_modules_init(1 0 0 QUALIFIER RC3)
webos_component(2 0 0)


# Consider adding to cmake-modules-webos after having it handle <path-to-basename>-s with .sh, etc. suffixes

# Usage: webos_build_configured_script(<path-to-basename> [ADMIN] [ROOTFS] [RESTRICTED_PERMISSIONS])
#
# Configures file <path-to-basename>.in and installs the resulting file in the program directory selected by the optional ADMIN and
# ROOTFS arguments.
# If <path-to-basename> is relative, CMAKE_SOURCE_DIR is prepended.
function(webos_build_configured_script basename)
	cmake_parse_arguments(webos_configured_script "ROOTFS;ADMIN;RESTRICTED_PERMISSIONS" "" "" ${ARGN})
	if(DEFINED webos_configured_script_UNPARSED_ARGUMENTS)
		message(FATAL_ERROR "(webos_build_configured_script(): Unrecognized arguments: '${webos_configured_script_UNPARSED_ARGUMENTS}'")
	endif()

	_webos_set_bin_inst_dir(destdir ${webos_configured_script_ADMIN} ${webos_configured_script_ROOTFS})
	_webos_set_bin_permissions(permissions ${webos_configured_script_RESTRICTED_PERMISSIONS})

	webos_make_source_path_absolute(basename "" TRUE)
	file(RELATIVE_PATH basename_relpath ${CMAKE_SOURCE_DIR} ${basename})
	set(configuredfile ${WEBOS_BINARY_CONFIGURED_DIR}/${basename_relpath})

	# XXX Ideally, ${configuredfile} would be read-only so that developers don't accidentally modify it
	configure_file(${basename}.in ${configuredfile} @ONLY)
	install(PROGRAMS ${configuredfile} DESTINATION ${destdir} ${permissions})
endfunction()

webos_build_configured_script(scripts/public/rdx_reporter)


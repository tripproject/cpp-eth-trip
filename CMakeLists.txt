#------------------------------------------------------------------------------
# Top-level CMake file for cpp-ethereum.
#
# The documentation for cpp-ethereum is hosted at http://cpp-ethereum.org
#
# ------------------------------------------------------------------------------
# This file is part of cpp-ethereum.
#
# cpp-ethereum is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# cpp-ethereum is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with cpp-ethereum.  If not, see <http://www.gnu.org/licenses/>
#
# (c) 2014-2016 cpp-ethereum contributors.
#------------------------------------------------------------------------------

cmake_minimum_required(VERSION 3.0.0)

set(ETH_CMAKE_DIR "${CMAKE_CURRENT_LIST_DIR}/cmake" CACHE PATH "The path to the cmake directory")
list(APPEND CMAKE_MODULE_PATH ${ETH_CMAKE_DIR})

set(CPP_ETHEREUM_DIR "${CMAKE_CURRENT_LIST_DIR}" CACHE PATH "Path to the root directory for cpp-ethereum")

# set cmake_policies
include(EthPolicy)
eth_policy()

# project name and version should be set after cmake_policy CMP0048
project(cpp-ethereum VERSION "1.3.0")

if (NOT WIN32)
    if (NOT EXISTS ${CMAKE_SOURCE_DIR}/evmjit/.git)
        message(FATAL_ERROR "Git submodules not initialized, execute:\n  git submodule update --init")
    endif()
endif()

set(CMAKE_SKIP_INSTALL_ALL_DEPENDENCY On)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)### trip

include(EthOptions)
include(EthCompilerSettings)
if (NOT WIN32)
    include(EthExecutableHelper)
endif()
include(EthDependencies)
include(EthUtils)

include(ProjectBoost)
include(ProjectCryptopp)
include(ProjectJsonCpp)
if (NOT WIN32)
    include(ProjectJsonRpcCpp)
endif()
include(ProjectSecp256k1)

if (WIN32)
    configure_project(MINIUPNPC)

else()
    configure_project(CPUID CURL EVMJIT FATDB MINIUPNPC ROCKSDB PARANOID TESTS VMTRACE)

    add_subdirectory(eth)
endif()
add_subdirectory(libdevcore)
add_subdirectory(libdevcrypto)
add_subdirectory(libethash)
add_subdirectory(libethashseal)
add_subdirectory(libethcore)
add_subdirectory(libethereum)
add_subdirectory(libevm)
add_subdirectory(libevmcore)
if (NOT WIN32)
    if (NOT EMSCRIPTEN)
        add_subdirectory(libp2p)
    endif()
    add_subdirectory(libweb3jsonrpc)
    add_subdirectory(libwebthree)
    add_subdirectory(libwhisper)

    if (EVMJIT)
        add_subdirectory(evmjit)
    endif()

    if (TOOLS)
        add_subdirectory(ethkey)
        add_subdirectory(ethminer)
        add_subdirectory(ethvm)
        if (NOT EMSCRIPTEN)
            add_subdirectory(rlp)
            add_subdirectory(bench)
        endif()
    endif()

    if (TESTS)
	    add_subdirectory(test)
    endif()
endif()

# TODO - split out json_spirit, libscrypt and sec256k1
add_subdirectory(utils)

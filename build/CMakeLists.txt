cmake_minimum_required(VERSION 2.6)

project(GRT C CXX)

# GRT library

#set the options
option(BUILD_EXAMPLES “build-examples” ON)

#Add any custom OS definitions
if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

if(APPLE)
endif()

if(LINUX)
endif()

#Set the main directory locations
set(GRT_SRC_DIR
    ${PROJECT_SOURCE_DIR}/../GRT
)

set(GRT_EXAMPLES_DIR
    ${PROJECT_SOURCE_DIR}/../examples
)

set(GRT_EXAMPLES_OUTPUT_DIR
    ${PROJECT_SOURCE_DIR}/examples
)

#Add all the project files
file(GLOB GRT_CLASSIFICATION_MODULES
    "${GRT_SRC_DIR}/ClassificationModules/AdaBoost/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/AdaBoost/WeakClassifiers/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/ANBC/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/BAG/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/DecisionTree/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/DTW/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/GMM/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/KNN/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/LDA/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/RandomForests/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/Softmax/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/SVM/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/SVM/LIBSVM/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/HMM/*.cpp"
    "${GRT_SRC_DIR}/ClassificationModules/MinDist/*.cpp"
)

file(GLOB GRT_CLUSTERING_MODULES
    "${GRT_SRC_DIR}/ClusteringModules/ClusterTree/*.cpp"
    "${GRT_SRC_DIR}/ClusteringModules/GaussianMixtureModels/*.cpp"
    "${GRT_SRC_DIR}/ClusteringModules/KMeans/*.cpp"
    "${GRT_SRC_DIR}/ClusteringModules/HierarchicalClustering/*.cpp"
    "${GRT_SRC_DIR}/ClusteringModules/SelfOrganizingMap/*.cpp"
)

file(GLOB GRT_CONTEXT_MODULES
    "${GRT_SRC_DIR}/ContextModules/*.cpp"
)

file(GLOB GRT_DATA_STRUCTURES
    "${GRT_SRC_DIR}/DataStructures/*.cpp"
)

file(GLOB GRT_FEATURE_EXTRACTION_MODULES
    "${GRT_SRC_DIR}/FeatureExtractionModules/FFT/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/KMeansFeatures/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/KMeansQuantizer/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/MovementIndex/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/MovementTrajectoryFeatures/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/RBMQuantizer/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/SOMQuantizer/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/TimeDomainFeatures/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/TimeseriesBuffer/*.cpp"
    "${GRT_SRC_DIR}/FeatureExtractionModules/ZeroCrossingCounter/*.cpp"
)

file(GLOB GRT_CORE_ALGORITHMS
    "${GRT_SRC_DIR}/CoreAlgorithms/BernoulliRBM/*.cpp"
    “${GRT_SRC_DIR}/CoreAlgorithms/MovementDetector/*.cpp”
    "${GRT_SRC_DIR}/CoreAlgorithms/PrincipalComponentAnalysis/*.cpp"
    "${GRT_SRC_DIR}/CoreAlgorithms/Tree/*.cpp"
)

file(GLOB GRT_PIPELINE
    "${GRT_SRC_DIR}/CoreModules/*.cpp"
)

file(GLOB GRT_POST_PROCESSING_MODULES
    "${GRT_SRC_DIR}/PostProcessingModules/*.cpp"
)

file(GLOB GRT_PRE_PROCESSING_MODULES
    "${GRT_SRC_DIR}/PreProcessingModules/*.cpp"
)

file(GLOB GRT_REGRESSION_MODULES
    "${GRT_SRC_DIR}/RegressionModules/ArtificialNeuralNetworks/MLP/*.cpp"
    "${GRT_SRC_DIR}/RegressionModules/LinearRegression/*.cpp"
    "${GRT_SRC_DIR}/RegressionModules/LogisticRegression/*.cpp"
    "${GRT_SRC_DIR}/RegressionModules/MultidimensionalRegression/*.cpp"
    "${GRT_SRC_DIR}/RegressionModules/RegressionTree/*.cpp"
)

file(GLOB GRT_UTIL
    "${GRT_SRC_DIR}/Util/*.cpp"
)

set(GRT_SRC
    ${GRT_CLASSIFICATION_MODULES}
    ${GRT_CLUSTERING_MODULES}
    ${GRT_CONTEXT_MODULES}
    ${GRT_DATA_STRUCTURES}
    ${GRT_FEATURE_EXTRACTION_MODULES}
    ${GRT_CORE_ALGORITHMS}
    ${GRT_PIPELINE}
    ${GRT_POST_PROCESSING_MODULES}
    ${GRT_PRE_PROCESSING_MODULES}
    ${GRT_REGRESSION_MODULES}
    ${GRT_UTIL}
)

file(GLOB_RECURSE GRT_HEADERS
    "${GRT_SRC_DIR}/*.h"
)

include_directories(
    "${GRT_SRC_DIR}"
)

add_library(grt STATIC
    ${GRT_SRC}
    ${GRT_HEADERS}
)

install(
    TARGETS grt
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION bin
    ARCHIVE DESTINATION lib
)

install(
    DIRECTORY "${GRT_SRC_DIR}/"
    DESTINATION include/GRT
    FILES_MATCHING PATTERN *.h
)

# GRT examples
if( BUILD_EXAMPLES )
	function(grt_add_example _example_name _example_sub_directory)
	    file(GLOB sources
	        "${GRT_EXAMPLES_DIR}/${_example_sub_directory}/${_example_name}/*.cpp"
	        "${GRT_EXAMPLES_DIR}/${_example_sub_directory}/${_example_name}/*.h"
	    )

	    if(sources)
	        add_executable(${_example_name}
	            ${sources}
	        )

	        target_link_libraries(${_example_name}
	            grt
	        )

	        install(
	            TARGETS ${_example_name}
	            RUNTIME DESTINATION ${GRT_EXAMPLES_OUTPUT_DIR}
	        )

	        string(REPLACE
	            "Example"           # match string
	            "TrainingData"      # replace string
	            TRAINING_DATA       # output
	            ${_example_name}    # input
	        )

	        if(EXISTS "${GRT_EXAMPLES_DIR}/${_example_sub_directory}/${_example_name}/${TRAINING_DATA}.grt")
	            install(FILES
	                "${GRT_EXAMPLES_DIR}/${_example_sub_directory}/${_example_name}/${TRAINING_DATA}.grt"
	                DESTINATION ${GRT_EXAMPLES_OUTPUT_DIR}
	            )
	        endif()
	    endif()
	endfunction()

	macro(grt_get_sub_directory_list _result _source_directory)
	    file(GLOB children
	        RELATIVE "${_source_directory}"
	        "${_source_directory}/*"
	    )

	    set(${_result} "")

	    foreach(child ${children})
	        if(IS_DIRECTORY "${_source_directory}/${child}")
	            list(APPEND ${_result} ${child})
	        endif()
	    endforeach()
	endmacro()

	grt_get_sub_directory_list(GRT_EXAMPLES_SUB_DIRECTORIES
	    "${GRT_EXAMPLES_DIR}"
	)

	foreach(GRT_EXAMPLES_SUB_DIRECTORY ${GRT_EXAMPLES_SUB_DIRECTORIES})
	    grt_get_sub_directory_list(GRT_EXAMPLES
	        "${GRT_EXAMPLES_DIR}/${GRT_EXAMPLES_SUB_DIRECTORY}"
	    )

	    foreach(GRT_EXAMPLE ${GRT_EXAMPLES})
	        grt_add_example(
	            ${GRT_EXAMPLE}
	            ${GRT_EXAMPLES_SUB_DIRECTORY}
	        )
	    endforeach()
	endforeach()
endif() #BUILD_EXAMPLES


Introduction
============

This repository (H01LocalConnectome) contains various structural/functional interrogations (and visualisations) of the H01 Release human connectome dataset, and software used in their derivation. The H01 "Local Connectome" is taken as the connectivity data between those neurons (neuron_id) which have cell soma/centroids that reside within the H01 Release dataset. The local connectome is analysed along with its immediate incoming and outgoing connections.

Acknowledgements
================

Source datasets are from the H01 Release of the Petascale Reconstruction of the Human Cortex, as provided by The Lichtman laboratory at Harvard University and the Connectomics at Google team (https://h01-release.storage.googleapis.com/landing.html):

*Shapson-Coe, A., Januszewski, M., Berger, D.R., Pope, A., Wu, Y., Blakely, T., Schalek, R.L., Li, P., Wang, S., Maitin-Shepard, J. et al. (2021). A connectomic study of a petascale fragment of human cerebral cortex. bioRxiv. (https://www.biorxiv.org/content/10.1101/2021.05.29.446289v2)*

**SVG plots of excitatory/inhibitory connections in local connectome** (connection types derived from EM images, directionality not encoded):
- https://storage.googleapis.com/h01-release/data/20210601/svg/connections_E.svg
- https://storage.googleapis.com/h01-release/data/20210601/svg/connections_I.svg

**C3 Synaptic connections database** - export of synaptic connections in Apache Avro/Json format (connection types derived from EM images, pre-post synaptic directionality encoded):
- gs://h01-release/data/20210601/c3/synapses/exported/json (https://h01-release.storage.googleapis.com/data.html)

**Local connectome dataset** as provided by Dr Yuelong Wu of the The Lichtman laboratory (includes cell centroids and cell types, pre-post synaptic directionality encoded):
- in_body_cell_connection.csv

Additional H01 Release datasets or updates thereto are expected. As of the bioRxiv v2 release of the H01 paper, the synapse predictions are still being refined so may be subject to future changes. The cell types, synapses types etc. were all generated by deep learning models trained by The Lichtman laboratory and their collaborators at Google, and they internally used Google BigQuery to share the data. Because the BigQuery tables are not discoverable by outside groups, they exported the synapse table to Avro format when releasing the data. There are other database (e.g. cell type table including coordinates, compartment type table, some of which are huge sized tables) which have not yet been released.

License
=======

All BAI H01LocalConnectome repository software is licensed with MIT License unless otherwise specified.

Content
=======

H01 indexed CSV database software
---------------------------------

*H01indexedCSVdatabase*

H01indexedCSVdatabase.cpp/.hpp:

 * Description: H01 indexed CSV database
 * Requirements: BAI SHARED C++ library, Eigen 3 C++ library
 * Compilation: ./compileH01indexedCSVdatabase.sh
 * Usage: ./H01indexedCSVdatabase.exe

H01indexedCSVdatabaseCreate.cpp/.hpp:

 * Description: H01 indexed CSV database create - convert C3 Synaptic connections Avro Json To indexed CSV database (indexed by pre/postsynaptic neuron ID)
 * Input: C3 Synaptic connections database (gs://h01-release/data/20210601/c3/synapses/exported/json)
 * Output Format: ssddata/indexed/123/csvPreSynapticNeuronID123456.csv - presynapticSiteNeuronID, postsynapticSiteNeuronID, presynapticSiteType, postsynapticSiteType, presynapticSiteClassLabel, postsynapticSiteClassLabel, presynapticSiteBaseNeuronID, postsynapticSiteBaseNeuronID, synapseLocationXcoordinates, synapseLocationYcoordinates, synapseLocationZcoordinates, synapseType

H01indexedCSVdatabaseQuery.cpp/.hpp:
 * Description: H01 indexed CSV database query - lookup indexed CSV database by pre/postsynaptic neuron ID, and find target/source connections
 * Input: 
     * INDEXED_CSV_DATABASE_QUERY_PERFORM_INCOMING_AXON_MAPPING: localConnectomeNeuronIDlistDistinct.csv
     * INDEXED_CSV_DATABASE_QUERY_OUTPUT_CONNECTIONS: localConnectomeNeuronIDlist1.csv/localConnectomeNeuronIDlist2.csv
 * Output Format (csv):
     * INDEXED_CSV_DATABASE_QUERY_PERFORM_INCOMING_AXON_MAPPING:
        * INDEXED_CSV_DATABASE_QUERY_PERFORM_INCOMING_AXON_MAPPING_3D_LINEAR_REGRESSION:
            * INDEXED_CSV_DATABASE_QUERY_OUTPUT_INCOMING_AXON_MAPPING_LDR: localConnectomeIncomingAxonMapping.ldr - LDR_TYPE_LINE ldrawColor plot3DpointStart.x plot3DpointStart.y plot3DpointStart.z plot3DpointEnd.x plot3DpointEnd.y plot3DpointEnd.z
             * INDEXED_CSV_DATABASE_QUERY_OUTPUT_INCOMING_AXON_MAPPING_CSV: localConnectomeIncomingAxonMapping.csv - polyFit.connectionNeuronID, polyFit.estSynapseType, polyFit.origin.x, polyFit.origin.y, polyFit.origin.z, polyFit.axis.x, polyFit.axis.y, polyFit.axis.z
        * INDEXED_CSV_DATABASE_QUERY_PERFORM_INCOMING_AXON_MAPPING_2D_POLY_REGRESSION:
             * INDEXED_CSV_DATABASE_QUERY_OUTPUT_INCOMING_AXON_MAPPING_CSV: localConnectomeIncomingAxonMapping.csv - polyFit.connectionNeuronID, polyFit.estSynapseType, polyFit.a, polyFit.b, polyFit.c
     * INDEXED_CSV_DATABASE_QUERY_OUTPUT_CONNECTIONS: localConnectomeNeuronIDlist1connectionsPresynaptic.csv/localConnectomeNeuronIDlist1connectionsPostsynaptic.csv/localConnectomeNeuronIDlist2connectionsPresynaptic.csv/localConnectomeNeuronIDlist2connectionsPostsynaptic.csv - connectionNeuronID1, connectionType1 [, locationObjectContentsXcoordinatesContent1, locationObjectContentsYcoordinatesContent1, locationObjectContentsZcoordinatesContent1], connectionNeuronID2, connectionType2 [, locationObjectContentsXcoordinatesContent2, locationObjectContentsYcoordinatesContent2, locationObjectContentsZcoordinatesContent2], etc 

Note LDView can be used to view 3D LDR visualisations (https://tcobbs.github.io/ldview/Downloads.html). A web browser/Inkscape/etc can be used to view 2D SVG visualisations.

Local connectome visualisations
-------------------------------

*connectionTypesFromEMimages*
*connectionTypesFromPresynapticNeurons*

Current local connectome visualisations for connectionTypesDerivedFromEMimages/connectionTypesDerivedFromPresynapticNeurons include:

- connections_IE.svg (connections_IE_Lx.svg) - connections coloured by excitatory/inhibitory type and pre/post synaptic direction; neurons colours by cell type.
- connections_IE_layered.svg (connections_IE_layeredLx.svg) - neurons/connections coloured by layer.
- connections_IE_flow.svg (connections_IE_flow_Lx.svg) - connections coloured by excitatory/inhibitory type and flow direction (through the cortical layers); neurons colours by cell type.
- connections_IE_flowIO.svg (connections_IE_flowIO_Lx.svg) - connections coloured by excitatory/inhibitory type, pre/post synaptic direction, and flow direction (through the cortical layers); neurons colours by cell type.

The colour sets can be modified/customised within the svg file <"linearGradient"> tags.


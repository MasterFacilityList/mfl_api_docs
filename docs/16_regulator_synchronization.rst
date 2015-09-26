Regulator Synchronization
================================

This is for facilities that are initially created in the RHRIS system and
do not have a master facility code assigned to them.
This endpoint provides a way to enable the regulatory system to notify
the master facility list(MFL) that there are facilities that have
been registered and they are not in the MFL. After which the concerned officer (CHRIO)
can ensure that the facilties are registered with the MFL.

The synchronization process:
++++++++++++++++++++++++++++++++

**The synchronization resource has the following **important** fields**

=================== ============= ========================================================
Field                 Required      Explanation
=================== ============= ========================================================
Name                   Yes          This is the name of the facility
Regisration_number     Yes          This the registration number as assigned by the regulator
County                 Yes          This is the code of the county where the facility is located
Owner                  Yes          The id of the owner as the per the MFL
Facility_type          Yes          The id of the facility type as per the MFL
Mfl_code               No           The mfl code assigned to the facility once it is created in MFL
=================== ============= ========================================================

**Obtaining the owner's ids**

The owner's ids can be obtained by doing a ``GET`` to the **URL** ``api/facilities/owners/``

Sample Expected Result:

 .. code-block:: javascript

    {
        "count": 30,
        "next": null,
        "previous": null,
        "page_size": 30,
        "current_page": 1,
        "total_pages": 1,
        "start_index": 1,
        "end_index": 30,
        "results": [
            {
                "id": "aa1aca14-2937-4b49-b1a5-3c1ce05895ae",
                "owner_type_name": "Faith Based Organization",
                "created": "2015-09-23T13:16:13.988038Z",
                "updated": "2015-09-23T13:16:13.988060Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Faith Based",
                "description": null,
                "code": 1013,
                "abbreviation": null,
                "created_by": 1,
                "updated_by": 1,
                "owner_type": "c35677b6-05a4-4233-9dfa-9544476850c4"
            },
            {
                "id": "aa32ee6f-3653-4fb8-bd2e-4e59b61a952c",
                "owner_type_name": "Ministry of Health",
                "created": "2015-09-23T13:16:13.961025Z",
                "updated": "2015-09-23T13:16:13.961049Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Ministry of Health",
                "description": null,
                "code": 1010,
                "abbreviation": null,
                "created_by": 1,
                "updated_by": 1,
                "owner_type": "33ebff77-f5fc-46dd-b675-7abd56d7bfdf"
            }

        ]
    }


Expected Response code:
    ``HTTP_200_OK``

**Obtaining the facility type ids**

The facility types's ids can be obtained by doing a **GET** to the **URL** ``api/facilities/facility_types/``
Sample Expected Result

 .. code-block:: javascript

    {
        "count": 41,
        "next": "http://localhost:8061/api/facilities/facility_types/?page=2",
        "previous": null,
        "page_size": 30,
        "current_page": 1,
        "total_pages": 2,
        "start_index": 1,
        "end_index": 30,
        "results": [
            {
                "id": "1ce27507-9bd0-43cf-8a6f-4519a018ad27",
                "owner_type_name": null,
                "created": "2015-09-23T13:16:13.438542Z",
                "updated": "2015-09-23T13:16:13.438562Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Laboratory (Stand-alone)",
                "abbreviation": null,
                "sub_division": null,
                "created_by": 1,
                "updated_by": 1,
                "owner_type": null,
                "preceding": null
            },
            {
                "id": "f9f5bd67-b679-4711-8752-d77c2397ddc9",
                "owner_type_name": null,
                "created": "2015-09-23T13:16:13.431970Z",
                "updated": "2015-09-23T13:16:13.431993Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Hospital",
                "abbreviation": null,
                "sub_division": null,
                "created_by": 1,
                "updated_by": 1,
                "owner_type": null,
                "preceding": null
            }
        ]
    }


Step 1
+++++++++++++
First the regulator system posts to MFL the details of the facilities
that have been created in the RHIS and are not in the MFL.
To do this do a ``POST`` to ``api/facilitiess/regulator_sync/`` a payload similiar to the one below:

 .. code-block:: javascript

    {
        "name": "Mama Lucy Kibaki hospital",
        "registration_number": 100,
        "county": 47,
        "owner": "aa1aca14-2937-4b49-b1a5-3c1ce05895ae",
        "facility_type": "f9f5bd67-b679-4711-8752-d77c2397ddc9"
    }

Sample Expected Response:

.. code-block:: javascript

    {
        "id": "817c8a79-a3e5-46b1-aba5-4cb4de78a5da",
        "county_name": "NAIROBI",
        "owner_name": "Other Faith Based",
        "facility_type_name": "Hospital",
        "created": "2015-09-26T09:38:12.801942Z",
        "updated": "2015-09-26T09:38:12.801959Z",
        "deleted": false,
        "active": true,
        "search": null,
        "name": "Mama Lucy Kibaki hospital",
        "registration_number": "100",
        "county": 47,
        "mfl_code": null,
        "created_by": 4,
        "updated_by": 4,
        "facility_type": "f9f5bd67-b679-4711-8752-d77c2397ddc9",
        "owner": "aa1aca14-2937-4b49-b1a5-3c1ce05895ae"
    }

Expected Response Code:

    ``HTTP_201_CREATED``


Step 2
+++++++++++++
Once a facility synchrionization has been initiated, the request to register a faciility will appear on the concerned CHRIO's dashboard.
On registration of the facility with the MFL the mfl_code will be field and the RHRIS can now pull and get a facility's mfl_code.

Listing of synchronized facilities
----------------------------------

To list the facilities requested do a ``GET`` to the URL ``api/facilities/regulatory_sync/``

Sample Expected Result:

.. code-block:: javascript

    {
        "count": 3,
        "next": null,
        "previous": null,
        "page_size": 30,
        "current_page": 1,
        "total_pages": 1,
        "start_index": 1,
        "end_index": 3,
        "results": [
            {
                "id": "817c8a79-a3e5-46b1-aba5-4cb4de78a5da",
                "county_name": "NAIROBI",
                "owner_name": "Other Faith Based",
                "facility_type_name": "Hospital",
                "created": "2015-09-26T09:38:12.801942Z",
                "updated": "2015-09-26T09:38:12.801959Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Mama Lucy Kibaki hospital",
                "registration_number": "100",
                "county": 47,
                "mfl_code": null,
                "created_by": 4,
                "updated_by": 4,
                "facility_type": "f9f5bd67-b679-4711-8752-d77c2397ddc9",
                "owner": "aa1aca14-2937-4b49-b1a5-3c1ce05895ae"
            },
            {
                "id": "94e91d84-6f73-48c1-855e-5a9539845971",
                "county_name": "GARISSA",
                "owner_name": "Private Practice - Medical Specialist",
                "facility_type_name": "Sub-District Hospital",
                "created": "2015-09-25T10:08:05.715148Z",
                "updated": "2015-09-25T10:08:05.715194Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Kamau Kiarie",
                "registration_number": "14535",
                "county": 7,
                "mfl_code": null,
                "created_by": 1,
                "updated_by": 1,
                "facility_type": "8b3b71b8-23ae-48a5-b7ee-e5078794c6c7",
                "owner": "a164b5bf-8caa-4558-9ba5-a77c87363b3d"
            },
            {
                "id": "f827f31d-aed0-4d63-90ad-7320769e4638",
                "county_name": "TAITA TAVETA",
                "owner_name": "Private Practice - Medical Specialist",
                "facility_type_name": "Sub-District Hospital",
                "created": "2015-09-25T10:07:54.192779Z",
                "updated": "2015-09-25T10:07:54.192817Z",
                "deleted": false,
                "active": true,
                "search": null,
                "name": "Mama Lucy",
                "registration_number": "14535",
                "county": 6,
                "mfl_code": null,
                "created_by": 1,
                "updated_by": 1,
                "facility_type": "8b3b71b8-23ae-48a5-b7ee-e5078794c6c7",
                "owner": "a164b5bf-8caa-4558-9ba5-a77c87363b3d"
            }
        ]
    }


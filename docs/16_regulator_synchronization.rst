Regulator Synchronization
==========================
Regulator synchronization is divided into two sections:
    #. :ref:`part_1-label`
    #. :ref:`part_2-label`

.. _part_1-label:

Part 1: Attached Facilities Synchronization
+++++++++++++++++++++++++++++++++++++++++++
Attached facilities are those facilities that offer specialized health services
such as pharmaceutical services, laboratory services, opthalmology services, physiotherapy
services etc besides offering other general health services.

This is for **Attached facilities** that are initially created in the RHRIS system and
do not have a master facility code assigned to them.
This endpoint provides a way to enable the regulatory system to notify
the master facility list(MFL) that there are facilities that have
been registered and they are not in the MFL. After which the concerned officer (CHRIO)
can ensure that the facilities are registered with the MFL.

The synchronization process:
-----------------------------

The regulator synchronization resource has the following **important** fields:

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

The facility type's ids can be obtained by doing a **GET** to the **URL** ``api/facilities/facility_types/``
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
-------
First the regulator system posts to MFL the details of the facilities
that have been created in the RHIS and are not in the MFL.
To do this do a ``POST`` to ``api/facilitiess/regulator_sync/`` a payload similar to the one below:

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
--------
Once a facility synchronization has been initiated, the request to register a facility will appear on the concerned CHRIO's dashboard.
On registration of the facility with the MFL the mfl_code will be field and the RHRIS can now pull and get a facility's mfl_code.

Listing of synchronized facilities
----------------------------------

To list the facilities requested do a ``GET`` to the URL ``api/facilities/regulator_sync/``

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


.. _part_2-label:

Part 2: Stand Alone Facilities Synchronization
++++++++++++++++++++++++++++++++++++++++++++++
Stand alone facilities are those facilities that offer only one specialized health care service.
e.g laboratories,  pharmacies, blood bank centers etc.

Synchronization process
------------------------
Stand alone facilities such as pharmacies are registered in the regulator systems and are inspected
and they start operating.
On final inspection, the facilities are pushed to MFL via the API:

Pushing a Facility Basic Details
________________________________
To push the details to MFL ``POST`` to ``api/facilities/facilities/`` a payload similar to the one below:

.. code-block:: javascript

     {
        "owner": "af7f2be2-3454-4ba8-ae01-d24c05cfb382",
        "name": "Rehema Pharmacy (Bahati)",
        "official_name": "Rehema Pharmacy",
        "registration_number": "PBB 12444",
        "open_whole_day": true,
        "open_public_holidays": false,
        "open_weekends": true,
        "open_late_night": false,
        "plot_number": "LR/14414/KEN",
        "location_desc": "Along Chiefs Road",
        "facility_type": "6bbfc198-23f0-4310-9170-24ac05e2e49e",
        "operation_status": "3f5634c7-5a47-4e1d-b2f5-8e9b2308acf0",
        "ward": "b530b7ed-a110-431f-9a19-847eb706792d",
        "regulatory_body": "e4ae432e-8a0a-402c-ab6c-1c9033102bb5",
        "town": "ee724c13-abfe-44cb-98ce-9ec36a1e97a9"
    }


The fields in the payload are explained below:

====================== ============= =============================================================================
 Field                  Required       Explanation
====================== ============= =============================================================================
 name                   Yes           This is the unique name of a facility e.g Agha Khan Medical Centre(Mombasa)
 official_name          Yes           This is the name of the facility e.g Agha Khan medical centre
 open_whole_day         No            Indicates whether a facility is open 24 hours a day
 open_public_holidays   No            Indicates whether a facility is open on public holidays
 open_late_night        No            Indicates whether a facility is open late night
 open_weekends          No            Indicates whether a facility is open on weekends
 plot_mumber            No            The plot number of where the facility is located
 location_desc          No            A description on how to access the facility e.g which road to use
 facility_type          Yes           This is the type of the facility <id> of the facility type e.f pharmacy
 operation_status       Yes           The operation status id e.g Operation Status Id
 ward                   Yes           The ward ID of where the facility is located
 regulatory_body        Yes           The regulatory body ID of the facility e.g Pharmacy and Poisons Board id
 town                   No            The id of the town or health centre where the facility is located
 registration_number    Yes           This the registration number as assigned by the regulator
 owner                  Yes           The id of the owner as the per the MFL
====================== ============= =============================================================================

Sample Expected Response:

.. code-block:: javascript

    {
        "id": "da3c4efe-57df-4d65-aa29-b6eb6719e469",
        "regulatory_status_name": "Pending License",
        "facility_type_name": "Pharmacy",
        "owner_name": "Private Practice - Unspecified",
        "owner_type_name": "Private Institutions and Private Practice",
        "owner_type": "2b8b031e-8d5a-47eb-b89c-a63d11e2b70a",
        "operation_status_name": "Operational",
        "county": "NAIROBI",
        "constituency": "MATHARE",
        "ward_name": "KIAMAIKO",
        "average_rating": 0,
        "facility_services": [],
        "is_approved": null,
        "has_edits": false,
        "latest_update": null,
        "regulatory_body_name": "Pharmacy & Poisons Board",
        "owner": "af7f2be2-3454-4ba8-ae01-d24c05cfb382",
        "date_requested": "2015-11-10T10:27:53.932Z",
        "date_approved": null,
        "latest_approval_or_rejection": null,
        "sub_county_name": null,
        "created": "2015-11-10T10:27:53.932878Z",
        "updated": "2015-11-10T10:27:53.932886Z",
        "deleted": false,
        "active": true,
        "search": null,
        "name": "Rehema Pharmacy (Bahati)",
        "official_name": "Rehema Pharmacy",
        "code": 100000,
        "registration_number": "PBB 12444",
        "abbreviation": null,
        "description": null,
        "number_of_beds": 0,
        "number_of_cots": 0,
        "open_whole_day": true,
        "open_public_holidays": false,
        "open_weekends": true,
        "open_late_night": false,
        "is_classified": false,
        "is_published": false,
        "attributes": null,
        "regulated": false,
        "approved": false,
        "rejected": false,
        "bank_name": null,
        "branch_name": null,
        "bank_account": null,
        "facility_catchment_population": null,
        "nearest_landmark": null,
        "plot_number": "LR/14414/KEN",
        "location_desc": "Along Chiefs Road",
        "closed": false,
        "closed_date": null,
        "closing_reason": null,
        "created_by": 4,
        "updated_by": 4,
        "facility_type": "6bbfc198-23f0-4310-9170-24ac05e2e49e",
        "operation_status": "3f5634c7-5a47-4e1d-b2f5-8e9b2308acf0",
        "ward": "b530b7ed-a110-431f-9a19-847eb706792d",
        "parent": null,
        "regulatory_body": "e4ae432e-8a0a-402c-ab6c-1c9033102bb5",
        "keph_level": null,
        "sub_county": null,
        "town": "ee724c13-abfe-44cb-98ce-9ec36a1e97a9",
        "contacts": []
    }

.. note::
    The MFL code assigned to the facility is in the response data (The field is ``code``).


Expected Response Code:

    ``HTTP_201_CREATED``



It is clear that there is data that needs to be mapped between MFL and the regulators in order for this work.

The data includes:
    #. Wards
    #. Owners
    #. Operation status
    #. Facility types
    #. Regulatory Bodies
    #. Towns


All the above data is explained under the :doc:`08_support_resources` section of the documentation.


Pushing a Facility Geo-location Details
_______________________________________
TODO

Pushing a Facility Contacts
___________________________
TODO

Pushing a Facility's Officer-In-Charge
______________________________________
TODO

Pushing a Facility's Services
_____________________________
TODO

.. toctree::
    :maxdepth: 2

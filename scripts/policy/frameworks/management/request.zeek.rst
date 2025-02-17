:tocdepth: 3

policy/frameworks/management/request.zeek
=========================================
.. zeek:namespace:: Management::Request

This module implements a request state abstraction in the Management
framework that both controller and agent use to connect request events to
subsequent response ones, and to be able to time out such requests.

:Namespace: Management::Request
:Imports: :doc:`policy/frameworks/management/config.zeek </scripts/policy/frameworks/management/config.zeek>`, :doc:`policy/frameworks/management/types.zeek </scripts/policy/frameworks/management/types.zeek>`

Summary
~~~~~~~
Redefinable Options
###################
=========================================================================================== ==============================
:zeek:id:`Management::Request::timeout_interval`: :zeek:type:`interval` :zeek:attr:`&redef` The timeout for request state.
=========================================================================================== ==============================

State Variables
###############
=================================================================================== ==========================================================
:zeek:id:`Management::Request::null_req`: :zeek:type:`Management::Request::Request` A token request that serves as a null/nonexistant request.
=================================================================================== ==========================================================

Types
#####
============================================================== ====================================================================
:zeek:type:`Management::Request::Request`: :zeek:type:`record` Request records track state associated with a request/response event
                                                               pair.
============================================================== ====================================================================

Events
######
=================================================================== ======================================================================
:zeek:id:`Management::Request::request_expired`: :zeek:type:`event` This event fires when a request times out (as per the
                                                                    Management::Request::timeout_interval) before it has been finished via
                                                                    Management::Request::finish().
=================================================================== ======================================================================

Functions
#########
================================================================ ========================================================================
:zeek:id:`Management::Request::create`: :zeek:type:`function`    This function establishes request state.
:zeek:id:`Management::Request::finish`: :zeek:type:`function`    This function marks a request as complete and causes Zeek to release
                                                                 its internal state.
:zeek:id:`Management::Request::is_null`: :zeek:type:`function`   This function is a helper predicate to indicate whether a given
                                                                 request is null.
:zeek:id:`Management::Request::lookup`: :zeek:type:`function`    This function looks up the request for a given request ID and returns
                                                                 it.
:zeek:id:`Management::Request::to_string`: :zeek:type:`function` For troubleshooting, this function renders a request record to a string.
================================================================ ========================================================================


Detailed Interface
~~~~~~~~~~~~~~~~~~
Redefinable Options
###################
.. zeek:id:: Management::Request::timeout_interval
   :source-code: policy/frameworks/management/request.zeek 40 40

   :Type: :zeek:type:`interval`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``10.0 secs``

   The timeout for request state. Such state (see the :zeek:see:`Management::Request`
   module) ties together request and response event pairs. The timeout causes
   its cleanup in the absence of a timely response. It applies both to
   state kept for client requests, as well as state in the agents for
   requests to the supervisor.

State Variables
###############
.. zeek:id:: Management::Request::null_req
   :source-code: policy/frameworks/management/request.zeek 43 43

   :Type: :zeek:type:`Management::Request::Request`
   :Default:

      ::

         {
            id=""
            parent_id=<uninitialized>
            results=[]
            finished=T
            supervisor_state=<uninitialized>
            set_configuration_state=<uninitialized>
            get_nodes_state=<uninitialized>
            test_state=<uninitialized>
         }


   A token request that serves as a null/nonexistant request.

Types
#####
.. zeek:type:: Management::Request::Request
   :source-code: policy/frameworks/management/request.zeek 17 33

   :Type: :zeek:type:`record`

      id: :zeek:type:`string`
         Each request has a hopfully unique ID provided by the requester.

      parent_id: :zeek:type:`string` :zeek:attr:`&optional`
         For requests that result based upon another request (such as when
         the controller sends requests to agents based on a request it
         received by the client), this specifies that original, "parent"
         request.

      results: :zeek:type:`Management::ResultVec` :zeek:attr:`&default` = ``[]`` :zeek:attr:`&optional`
         The results vector builds up the list of results we eventually
         send to the requestor when we have processed the request.

      finished: :zeek:type:`bool` :zeek:attr:`&default` = ``F`` :zeek:attr:`&optional`
         An internal flag to track whether a request is complete.

      supervisor_state: :zeek:type:`Mangement::Agent::Runtime::SupervisorState` :zeek:attr:`&optional`
         (present if :doc:`/scripts/policy/frameworks/management/agent/main.zeek` is loaded)


      set_configuration_state: :zeek:type:`Management::Controller::Runtime::SetConfigurationState` :zeek:attr:`&optional`
         (present if :doc:`/scripts/policy/frameworks/management/controller/main.zeek` is loaded)


      get_nodes_state: :zeek:type:`Management::Controller::Runtime::GetNodesState` :zeek:attr:`&optional`
         (present if :doc:`/scripts/policy/frameworks/management/controller/main.zeek` is loaded)


      test_state: :zeek:type:`Management::Controller::Runtime::TestState` :zeek:attr:`&optional`
         (present if :doc:`/scripts/policy/frameworks/management/controller/main.zeek` is loaded)


   Request records track state associated with a request/response event
   pair. Calls to
   :zeek:see:`Management::Request::create` establish such state
   when an entity sends off a request event, while
   :zeek:see:`Management::Request::finish` clears the state when
   a corresponding response event comes in, or the state times out.

Events
######
.. zeek:id:: Management::Request::request_expired
   :source-code: policy/frameworks/management/controller/main.zeek 557 602

   :Type: :zeek:type:`event` (req: :zeek:type:`Management::Request::Request`)

   This event fires when a request times out (as per the
   Management::Request::timeout_interval) before it has been finished via
   Management::Request::finish().
   

   :req: the request state that is expiring.
   

Functions
#########
.. zeek:id:: Management::Request::create
   :source-code: policy/frameworks/management/request.zeek 107 112

   :Type: :zeek:type:`function` (reqid: :zeek:type:`string` :zeek:attr:`&default` = ``9Ye7pQPhuMe`` :zeek:attr:`&optional`) : :zeek:type:`Management::Request::Request`

   This function establishes request state.
   

   :reqid: the identifier to use for the request.
   

.. zeek:id:: Management::Request::finish
   :source-code: policy/frameworks/management/request.zeek 122 133

   :Type: :zeek:type:`function` (reqid: :zeek:type:`string`) : :zeek:type:`bool`

   This function marks a request as complete and causes Zeek to release
   its internal state. When the request does not exist, this does
   nothing.
   

   :reqid: the ID of the request state to releaase.
   

.. zeek:id:: Management::Request::is_null
   :source-code: policy/frameworks/management/request.zeek 135 141

   :Type: :zeek:type:`function` (request: :zeek:type:`Management::Request::Request`) : :zeek:type:`bool`

   This function is a helper predicate to indicate whether a given
   request is null.
   

   :request: a Request record to check.
   

   :returns: T if the given request matches the null_req instance, F otherwise.
   

.. zeek:id:: Management::Request::lookup
   :source-code: policy/frameworks/management/request.zeek 114 120

   :Type: :zeek:type:`function` (reqid: :zeek:type:`string`) : :zeek:type:`Management::Request::Request`

   This function looks up the request for a given request ID and returns
   it. When no such request exists, returns Management::Request::null_req.
   

   :reqid: the ID of the request state to retrieve.
   

.. zeek:id:: Management::Request::to_string
   :source-code: policy/frameworks/management/request.zeek 143 162

   :Type: :zeek:type:`function` (request: :zeek:type:`Management::Request::Request`) : :zeek:type:`string`

   For troubleshooting, this function renders a request record to a string.
   

   :request: the request to render.
   



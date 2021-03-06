========
 桶操作
========

PUT Bucket
----------

Creates a new bucket. To create a bucket, you must have a user ID and a valid AWS Access Key ID to authenticate requests. You may not
create buckets as an anonymous user.

.. note:: We do not support request entities for ``PUT /{bucket}`` in this release.

Constraints
~~~~~~~~~~~
In general, bucket names should follow domain name constraints.

- Bucket names must be unique.
- Bucket names must begin and end with a lowercase letter.
- Bucket names may contain a dash (-).

语法
~~~~

::

    PUT /{bucket} HTTP/1.1
    Host: cname.domain.com
    x-amz-acl: public-read-write

    Authorization: AWS {access-key}:{hash-of-header-and-secret}

参数
~~~~

+---------------+----------------------+-----------------------------------------------------------------------------+------------+
| Name          | Description          | Valid Values                                                                | Required   |
+===============+======================+=============================================================================+============+
| ``x-amz-acl`` | Canned ACLs.         | ``private``, ``public-read``, ``public-read-write``, ``authenticated-read`` | No         |
+---------------+----------------------+-----------------------------------------------------------------------------+------------+


HTTP 响应
~~~~~~~~~

If the bucket name is unique, within constraints and unused, the operation will succeed.
If a bucket with the same name already exists and the user is the bucket owner, the operation will succeed.
If the bucket name is already in use, the operation will fail.

+---------------+-----------------------+----------------------------------------------------------+
| HTTP Status   | Status Code           | Description                                              |
+===============+=======================+==========================================================+
| ``409``       | BucketAlreadyExists   | Bucket already exists under different user's ownership.  |
+---------------+-----------------------+----------------------------------------------------------+

DELETE Bucket
-------------

Deletes a bucket. You can reuse bucket names following a successful bucket removal.

Syntax
~~~~~~

::

    DELETE /{bucket} HTTP/1.1
    Host: cname.domain.com

    Authorization: AWS {access-key}:{hash-of-header-and-secret}

HTTP Response
~~~~~~~~~~~~~

+---------------+---------------+------------------+
| HTTP Status   | Status Code   | Description      |
+===============+===============+==================+
| ``204``       | No Content    | Bucket removed.  |
+---------------+---------------+------------------+


GET Bucket
----------
返回一系列桶对象。

语法
~~~~

::

    GET /{bucket}?max-keys=25 HTTP/1.1
    Host: cname.domain.com

参数
~~~~

+---------------------+-----------+-------------------------------------------------------------------------------------------------+
| Name                | Type      | Description                                                                                     |
+=====================+===========+=================================================================================================+
| ``prefix``          | String    | Only returns objects that contain the specified prefix.                                         |
+---------------------+-----------+-------------------------------------------------------------------------------------------------+
| ``delimiter``       | String    | The delimiter between the prefix and the rest of the object name.                               |
+---------------------+-----------+-------------------------------------------------------------------------------------------------+
| ``marker``          | String    | A beginning index for the list of objects returned.                                             |
+---------------------+-----------+-------------------------------------------------------------------------------------------------+
| ``max-keys``        | Integer   | The maximum number of keys to return. Default is 1000.                                          |
+---------------------+-----------+-------------------------------------------------------------------------------------------------+
| ``allow-unordered`` | Boolean   | Non-standard extension. Allows results to be returned unordered. Cannot be used with delimiter. |
+---------------------+-----------+-------------------------------------------------------------------------------------------------+


HTTP Response
~~~~~~~~~~~~~

+---------------+---------------+--------------------+
| HTTP Status   | Status Code   | Description        |
+===============+===============+====================+
| ``200``       | OK            | Buckets retrieved  |
+---------------+---------------+--------------------+

Bucket Response Entities
~~~~~~~~~~~~~~~~~~~~~~~~
``GET /{bucket}`` returns a container for buckets with the following fields.

+------------------------+-----------+----------------------------------------------------------------------------------+
| Name                   | Type      | Description                                                                      |
+========================+===========+==================================================================================+
| ``ListBucketResult``   | Entity    | The container for the list of objects.                                           |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``Name``               | String    | The name of the bucket whose contents will be returned.                          |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``Prefix``             | String    | A prefix for the object keys.                                                    |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``Marker``             | String    | A beginning index for the list of objects returned.                              |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``MaxKeys``            | Integer   | The maximum number of keys returned.                                             |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``Delimiter``          | String    | If set, objects with the same prefix will appear in the ``CommonPrefixes`` list. |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``IsTruncated``        | Boolean   | If ``true``, only a subset of the bucket's contents were returned.               |
+------------------------+-----------+----------------------------------------------------------------------------------+
| ``CommonPrefixes``     | Container | If multiple objects contain the same prefix, they will appear in this list.      |
+------------------------+-----------+----------------------------------------------------------------------------------+

Object Response Entities
~~~~~~~~~~~~~~~~~~~~~~~~
The ``ListBucketResult`` contains objects, where each object is within a ``Contents`` container.

+------------------------+-----------+------------------------------------------+
| Name                   | Type      | Description                              |
+========================+===========+==========================================+
| ``Contents``           | Object    | A container for the object.              |
+------------------------+-----------+------------------------------------------+
| ``Key``                | String    | The object's key.                        |
+------------------------+-----------+------------------------------------------+
| ``LastModified``       | Date      | The object's last-modified date/time.    |
+------------------------+-----------+------------------------------------------+
| ``ETag``               | String    | An MD-5 hash of the object. (entity tag) |
+------------------------+-----------+------------------------------------------+
| ``Size``               | Integer   | The object's size.                       |
+------------------------+-----------+------------------------------------------+
| ``StorageClass``       | String    | Should always return ``STANDARD``.       |
+------------------------+-----------+------------------------------------------+


Get Bucket Location
-------------------
Retrieves the bucket's region. The user needs to be the bucket owner
to call this. A bucket can be constrained to a region by providing
``LocationConstraint`` during a PUT request.

Syntax
~~~~~~
Add the ``location`` subresource to bucket resource as shown below

::

   GET /{bucket}?location HTTP/1.1
   Host: cname.domain.com

   Authorization: AWS {access-key}:{hash-of-header-and-secret}

Response Entities
~~~~~~~~~~~~~~~~~~~~~~~~

+------------------------+-----------+------------------------------------------+
| Name                   | Type      | Description                              |
+========================+===========+==========================================+
| ``LocationConstraint`` | String    | The region where bucket resides, empty   |
|                        |           | string for defult region                 |
+------------------------+-----------+------------------------------------------+


Get Bucket ACL
--------------
Retrieves the bucket access control list. The user needs to be the bucket
owner or to have been granted ``READ_ACP`` permission on the bucket.

Syntax
~~~~~~
Add the ``acl`` subresource to the bucket request as shown below.

::

    GET /{bucket}?acl HTTP/1.1
    Host: cname.domain.com

    Authorization: AWS {access-key}:{hash-of-header-and-secret}

Response Entities
~~~~~~~~~~~~~~~~~

+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| Name                      | Type        | Description                                                                                  |
+===========================+=============+==============================================================================================+
| ``AccessControlPolicy``   | Container   | A container for the response.                                                                |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``AccessControlList``     | Container   | A container for the ACL information.                                                         |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Owner``                 | Container   | A container for the bucket owner's ``ID`` and ``DisplayName``.                               |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``ID``                    | String      | The bucket owner's ID.                                                                       |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``DisplayName``           | String      | The bucket owner's display name.                                                             |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Grant``                 | Container   | A container for ``Grantee`` and ``Permission``.                                              |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Grantee``               | Container   | A container for the ``DisplayName`` and ``ID`` of the user receiving a grant of permission.  |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Permission``            | String      | The permission given to the ``Grantee`` bucket.                                              |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+

PUT Bucket ACL
--------------
Sets an access control to an existing bucket. The user needs to be the bucket
owner or to have been granted ``WRITE_ACP`` permission on the bucket.

Syntax
~~~~~~
Add the ``acl`` subresource to the bucket request as shown below.

::

    PUT /{bucket}?acl HTTP/1.1

Request Entities
~~~~~~~~~~~~~~~~

+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| Name                      | Type        | Description                                                                                  |
+===========================+=============+==============================================================================================+
| ``AccessControlPolicy``   | Container   | A container for the request.                                                                 |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``AccessControlList``     | Container   | A container for the ACL information.                                                         |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Owner``                 | Container   | A container for the bucket owner's ``ID`` and ``DisplayName``.                               |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``ID``                    | String      | The bucket owner's ID.                                                                       |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``DisplayName``           | String      | The bucket owner's display name.                                                             |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Grant``                 | Container   | A container for ``Grantee`` and ``Permission``.                                              |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Grantee``               | Container   | A container for the ``DisplayName`` and ``ID`` of the user receiving a grant of permission.  |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+
| ``Permission``            | String      | The permission given to the ``Grantee`` bucket.                                              |
+---------------------------+-------------+----------------------------------------------------------------------------------------------+

List Bucket Multipart Uploads
-----------------------------

``GET /?uploads`` returns a list of the current in-progress multipart uploads--i.e., the application initiates a multipart upload, but
the service hasn't completed all the uploads yet.

Syntax
~~~~~~

::

    GET /{bucket}?uploads HTTP/1.1

Parameters
~~~~~~~~~~

You may specify parameters for ``GET /{bucket}?uploads``, but none of them are required.

+------------------------+-----------+--------------------------------------------------------------------------------------+
| Name                   | Type      | Description                                                                          |
+========================+===========+======================================================================================+
| ``prefix``             | String    | Returns in-progress uploads whose keys contains the specified prefix.                |
+------------------------+-----------+--------------------------------------------------------------------------------------+
| ``delimiter``          | String    | The delimiter between the prefix and the rest of the object name.                    |
+------------------------+-----------+--------------------------------------------------------------------------------------+
| ``key-marker``         | String    | The beginning marker for the list of uploads.                                        |
+------------------------+-----------+--------------------------------------------------------------------------------------+
| ``max-keys``           | Integer   | The maximum number of in-progress uploads. The default is 1000.                      |
+------------------------+-----------+--------------------------------------------------------------------------------------+
| ``max-uploads``        | Integer   | The maximum number of multipart uploads. The range from 1-1000. The default is 1000. |
+------------------------+-----------+--------------------------------------------------------------------------------------+
| ``upload-id-marker``   | String    | Ignored if ``key-marker`` isn't specified. Specifies the ``ID`` of first             |
|                        |           | upload to list in lexicographical order at or following the ``ID``.                  |
+------------------------+-----------+--------------------------------------------------------------------------------------+


Response Entities
~~~~~~~~~~~~~~~~~

+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| Name                                    | Type        | Description                                                                                              |
+=========================================+=============+==========================================================================================================+
| ``ListMultipartUploadsResult``          | Container   | A container for the results.                                                                             |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``ListMultipartUploadsResult.Prefix``   | String      | The prefix specified by the ``prefix`` request parameter (if any).                                       |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Bucket``                              | String      | The bucket that will receive the bucket contents.                                                        |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``KeyMarker``                           | String      | The key marker specified by the ``key-marker`` request parameter (if any).                               |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``UploadIdMarker``                      | String      | The marker specified by the ``upload-id-marker`` request parameter (if any).                             |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``NextKeyMarker``                       | String      | The key marker to use in a subsequent request if ``IsTruncated`` is ``true``.                            |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``NextUploadIdMarker``                  | String      | The upload ID marker to use in a subsequent request if ``IsTruncated`` is ``true``.                      |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``MaxUploads``                          | Integer     | The max uploads specified by the ``max-uploads`` request parameter.                                      |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Delimiter``                           | String      | If set, objects with the same prefix will appear in the ``CommonPrefixes`` list.                         |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``IsTruncated``                         | Boolean     | If ``true``, only a subset of the bucket's upload contents were returned.                                |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Upload``                              | Container   | A container for ``Key``, ``UploadId``, ``InitiatorOwner``, ``StorageClass``, and ``Initiated`` elements. |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Key``                                 | String      | The key of the object once the multipart upload is complete.                                             |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``UploadId``                            | String      | The ``ID`` that identifies the multipart upload.                                                         |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Initiator``                           | Container   | Contains the ``ID`` and ``DisplayName`` of the user who initiated the upload.                            |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``DisplayName``                         | String      | The initiator's display name.                                                                            |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``ID``                                  | String      | The initiator's ID.                                                                                      |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Owner``                               | Container   | A container for the ``ID`` and ``DisplayName`` of the user who owns the uploaded object.                 |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``StorageClass``                        | String      | The method used to store the resulting object. ``STANDARD`` or ``REDUCED_REDUNDANCY``                    |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``Initiated``                           | Date        | The date and time the user initiated the upload.                                                         |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``CommonPrefixes``                      | Container   | If multiple objects contain the same prefix, they will appear in this list.                              |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+
| ``CommonPrefixes.Prefix``               | String      | The substring of the key after the prefix as defined by the ``prefix`` request parameter.                |
+-----------------------------------------+-------------+----------------------------------------------------------------------------------------------------------+

ENABLE/SUSPEND BUCKET VERSIONING
--------------------------------

``PUT /?versioning`` 这个子资源用于设置已有桶的版本化状态。只有桶\
所有者才能设置版本化状态。

版本化状态可以设置为如下取值之一：

- Enabled : 为桶内的对象启用版本化支持，放入桶内的对象都会收到一个\
  唯一的版本 ID 。
- Suspended : 为桶内的对象禁用版本化支持，放入桶内的对象其版本 ID \
  都是 null 。

如果从没给某一个桶设置过版本化状态，那它就没有版本化状态； \
GET versioning 请求就不会返回版本化状态值。


语法
~~~~

::

	PUT  /{bucket}?versioning  HTTP/1.1


请求实体
~~~~~~~~

+-----------------------------+-----------+------------------------------------------------+
| 名称                        | 类型      | 描述                                           |
+=============================+===========+================================================+
| ``VersioningConfiguration`` | Container | 用于包装此请求的容器。                         |
+-----------------------------+-----------+------------------------------------------------+
| ``Status``                  | String    | 设置桶的版本化状态，可用值： Suspended/Enabled |
+-----------------------------+-----------+------------------------------------------------+

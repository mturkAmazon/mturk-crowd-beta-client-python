Prerequisites
-------------
To use this library, you'll need first follow our `account set-up instructions`_
to set up your Mechanical Turk and AWS accounts to call the API.

Instantiating the client requires using a Session object from boto3 to
authenticate. The `boto3 docs`_ describe how to manage your credentials in more
detail.

.. _`account set-up instructions`: https://blog.mturk.com/setting-up-your-mechanical-turk-and-aws-accounts-to-call-the-sentiment-analysis-api-bd4e2a034c5
.. _`boto3 docs`: http://boto3.readthedocs.io/en/latest/guide/configuration.html

Quick Start
-----------
First, install the library:

.. code-block:: sh

    $ pip install mturk-crowd-beta-client

Then, from a Python interpreter or script, create your first task:

.. code-block:: python

    from mturk_crowd_beta_client import MTurkCrowdClient
    from boto3.session import Session
    import uuid

    # This examples assume you have a local AWS profile called
    # 'mturk-crowd-caller', but you can authenticate however you like,
    # including by directly passing in your access key and secret key.
    session = Session(profile_name='mturk-crowd-caller')

    # Create the client
    crowd_client = MTurkCrowdClient(session)

    # For this example, we'll give our task a random, unique name. For real
    # work, you'll probably want to pick a name based on your input source.
    task_name = 'my-test-task-' + uuid.uuid4().hex
    function_name = 'sentiment-analysis-test'

    # Create the task
    put_result = crowd_client.put_task(function_name,
                                       task_name,
                                       {'text': 'Everything is wonderful.'})
    print('PUT response: {}'.format(
        {'status_code': put_result.status_code, 'task': put_result.json()}))
    # => PUT response: {
    #  'status_code': 201,
    #  'task': {'input': {'text': 'Everything is wonderful.'},
    #           'problemDetails': None,
    #           'result': None,
    #           'state': 'processing',
    #           'taskName': 'my-test-task-73fbfb29f2bc451d9696d11103dcaf0e'}
    #   }

    # Get the task we just created. Note that for a production (i.e., non-test)
    # task, we'd have to poll periodically until the task completed.
    get_result = crowd_client.get_task(function_name, task_name)
    print('GET response: {}'.format(
        {'status_code': get_result.status_code, 'task': get_result.json()}))
    # => GET response: {
    #  'status_code': 200,
    #  'task': {'input': {'text': 'Everything is wonderful.'},
    #           'problemDetails': None,
    #           'result': {'sentiment': 'positive'}
    #           'state': 'completed',
    #           'taskName': 'my-test-task-73fbfb29f2bc451d9696d11103dcaf0e'}
    #   }

Further Reading
---------------

Check out our `usage instructions`_ and `API documentation`_ for more details
about how to use the API and this client.

.. _`usage instructions`: https://medium.com/@mechanicalturk/instructions-for-calling-our-new-sentiment-analysis-api-21986c2cdff0
.. _`API documentation`: https://medium.com/@mechanicalturk/mechanical-turk-sentiment-analysis-api-documentation-e0ab8279a70a

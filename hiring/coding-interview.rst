Coding interview
================

First a note on statistics. We conduct many coding interviews and relatively few
of them are good, although generally more than the take home coding exercise.
After a few of the early stages, the take home coding exercise and coding
interview is where many candidates exit the hiring process. Don’t be
disheartened if many of the coding interviews are poor. The reason we have the
stage is to help make sure the people we hire are great coders!

The coding interview follows on from the take home exercise review. The
following document describes how the take home exercise review is done:
:ref:`coding-take-home-exercise-review`

Here is a template to help you conduct the interview. Please copy it and don’t
add anything candidate specific to the template:
`Python Coding Interview Template <https://docs.google.com/document/d/1yzmifHye3xaW86gkcHNSwV5kZarBWzVYIB8EuASTi3A/edit?usp=sharing>`_

The coding interview is broken into 2 parts, both lasting about 25 minutes
(leaving 10 minutes for introduction and conclusion). The first part reviews the
feedback on the take home exercise. The second part is a live coding exercise.
The following sections describe both in detail.

It can be helpful to read through the written interview to gauge the level of
experience the candidate has (e.g., more senior versus just graduated).

Take Home Exercise Review
-------------------------

This part of the interview is like a pull request review of the take home
exercise. The public notes should include feedback from the reviewers of the
take home exercise. It is worth familiarizing yourself with the coding
submission so that you know the code before the interview. You may also want to
add other questions in addition to what the reviewers have already noted.

Discuss any comments or feedback you have on the code with the candidate to give
the candidate an opportunity to explain the reasoning behind making those
decisions and to gauge how the candidate reacts to feedback. Examples of what
could be concerning are (not an exhaustive list):

* Dismissing feedback without consideration
* Taking feedback personally rather than objectively considering an optimal
  implementation
* Lack of a good reason for making sub-optimal decisions (e.g., due to time
  constraints is reasonable, due to a lack of knowledge could be problematic)

Capture the questions and a summary of the candidate’s responses for the
scorecard.

Pair Programming Exercise
-------------------------

This part of the interview gives the candidate the opportunity to solve a
problem live. The following is a suggested runsheet (note that most candidates
won’t make it all the way through which is ok, we will get a good impression of
the candidate’s coding abilities anyway):

#. Share the first part of the code over chat (don’t share the find_secrets code
   yet), it might require more than one message due to message length
   limitations on some video call clients. Files can’t be easily shared so it is
   generally preferable to use the chat function.
#. Give the candidate an overview of the purpose of the class. For example, it
   is a basic implementation of a secret store product of a cloud provider. It
   should be able to store and return secrets. It is a shared store so more than
  one user may interact with the store. Any interactions with the store can be
   assumed to be authenticated (the user can be assumed to be who they claim to
   be) but not authorized (the store needs to check whether the user should be
   able to access a given secret). There are 2 classes of users, a normal user
   who should only be able to access secrets they have created and an admin user
   (indicated by the “admin” username) who should be able to access any secret
   by its id. The same secret value can be stored multiple times where each
   secret should have their own secret id.
#. Give the candidate an opportunity to read through the code and ask any
   questions. Be generous when answering questions although limit giving the
   candidate hints. If they ask you an implementation question it can help to
   ask them how they would do this implementation.
#. Ask the candidate to share their screen and start implementing the
   store_secret and get_secret function. Encourage them to talk as they are
   thinking and implementing to help you understand their thought process.
#. If you spot a mistake or an opportunity for the candidate to make an
   improvement, ask the candidate some questions. For example, why did you pick
   that data structure? You will need to balance how much guidance you give the
   candidate although it is better to help the candidate to move the interview
   along rather than them getting stuck on something small. It is also worth
   pointing out a mistake and giving the candidate a chance to improve rather
   than letting the candidate make a mistake without comment. Be sure to note
   any mistakes you spot or opportunities for them to do better on the notes
   including how the candidate responded to you pointing it out.
#. Once the candidate has progressed with the implementation (although before
   they are finished), give them a new requirement that multiple users should be
   able to be associated with the same secret. Ask them to adapt their
   implementation of store_secret and get_secret to support this, although they
   are not required to implement a way to associate another user to the same
   secret. If they happen to ask you about whether multiple users can be
   associated with the same secret on their own, share the information at that
   time. Be sure to note this in the document, it is a good quality to
   proactively ask questions to identify hidden requirements!
#. Once the implementation of store_secret and get_secret is complete, ask the
   candidate to write a few tests. This can be done in the same file as the
   other code. Optionally get them to run the tests, although you should be able
   to tell whether the tests will work by looking at them.
#. Once the tests are reasonably completed and you still have time left, share
   the find_secrets code with them. This function should return all the secrets
   the user has access to as an iterable. This is a bonus section and gives
   candidates an opportunity to shine.

The candidate should lead the implementation although if the candidate is stuck
it is reasonable to help the candidate or give them pointers. If the candidate
did need help or pointers, be sure to take a note, there is space for that just
after the scorecard in the template. The following are some of the key decisions
a candidate has to make in the implementation:

.. list-table::
    :widths: 20 40 40
    :header-rows: 1

    * - Decision
      - Poor Examples
      - Good Examples
    * - Choice of data structure to store secrets
      - List - retrieving a secret from a list is not time efficient. Dictionary
        keyed with something other than secret_id - get_secret needs to retrieve
        the secret by id. Storing associated users as a list - it is not
        efficient to check for list membership.
      - Dictionary keyed with the secret id with items as dictionaries with a
        value and users key where the users key is a set of users that have
        access to the key. Even better would be a dataclass or namedtuple for
        the items.
    * - Storing the secret store as a class or instance variable
      - Storing the secret store as a class variable (i.e., outside of the
        `__init__` function) - this means the secret store is shared among all
        instances of the store
      - Storing the secret store inside the `__init__` function
    * - Generating the secret_id
      - A hash based on the secret - this leaks the secret since the secret_id
        is not a sensitive value and also might cause clashes if storing the
        same secret multiple times. A random value - this could lead to clashes.
        Using the user id - this means the user can only store 1 secret.
      - Using the uuid.uuid4 function converted to a string. Alternatively,
        based on the number of secrets already in the store, although uuid is
        preferred.
    * - Ignoring the type hints
      - Returning the wrong types
      - Returning the right types
    * - Whether to keep the NotImplementedError
      - Keeping it in the code after any return statement
      - Removing it once the implementation has been completed
    * - Handling of the admin user
      - Not implementing the requirement or adding the admin to the user list
        for each secret
      - Storing the admin username as configuration e.g., on the class and
        checking the user for that value when retrieving the secret
    * - How to handle secret_id misses
      - Returning None or an empty string when the secret_id is not matched or
        the user is unauthorized
      - Raising an error (e.g., KeyError)
    * - What error to raise
      - Raising Exception
      - Raising a more specific error

At the end of the interview, take a screenshot of the code the candidate has
written and insert it into the interview document. Some candidates might suggest
that they will send it to you later, please don’t agree to this as they might
spend more time working on the code and delays you being able to complete your
scorecard after the interview. Complete the scorecard and include their score
out of the max score along with your notes in the scorecard on greenhouse. Also
include a link to the document with all the detailed notes you have taken. That
document should not be stored on a shared drive as those not involved in the
hiring decisions may have access to that location.

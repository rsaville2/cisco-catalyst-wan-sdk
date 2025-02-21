<p align="center">
  <a href="#"><img src="../images/catalystwan.svg" alt="Cisco Catalyst WAN SDK Logo" style="height:150px" />
</p>

## SDK Users Guide

This document is designed to serve as a guide to understand the cisco-catalystwan-sdk and how to use the SDK to facilitate the development of applications which ultimately provide automation for the Cisco Catalyst SD-WAN solution.

The cisco-catalystwan-sdk is designed to make it easier for developers to consume Catalyst SD-WAN Manager APIs.  It does this by providing abstractions for low-level tasks such as authentication and session management; as well implementing a data model for endpoints, so developers do not have parse JSON output to extract relevant information from returned objects.

## Authentication and Session Establishment

As with the Catalyst SD-WAN Manager REST-based APIs, catalystwan-sdk API access control is also based on sessions.  

The most direct way of establishing a session is to create a <b>ManageSession</b> object.  This can be done by calling the <b>create_manager_session()</b> method after importing it from the <b>session</b> module within the <b>catalystwan</b> package.  

![image](https://github.com/user-attachments/assets/829caebf-d62c-47de-b729-14b2a8bae6d7)

The <b>create_manager_session()</b> method takes three required parameters – a URL representing the IP address or domain name of the Catalyst SD-WAN Manager, a username, and a password.  Note that the username must have sufficient privileges on the Catalyst SD-WAN Manager to execute the required Catalyst SD-WAN Manager API calls.  Three optional parameters – port, subdomain, and logger – can also be included.

A detailed look a the create_manager_session() method is shown below.

```python
def create_manager_session(
    url: str,
    username: str,
    password: str,
    port: Optional[int] = None,
    subdomain: Optional[str] = None,
    logger: Optional[logging.Logger] = None,
) -> ManagerSession:
    """Factory method that creates session object and performs login according to parameters

    Args:
        url (str): IP address or domain name
        username (str): username
        password (str): password
        port (int): port
        subdomain: subdomain specifying to which view switch when creating provider as a tenant session,
            works only on provider user mode
        logger: override default module logger

    Returns:
        ManagerSession: logged-in and operative session to perform tasks on SDWAN Manager.
    """
    auth = create_vmanage_auth(username, password, subdomain, logger)
    session = ManagerSession(
        base_url=create_base_url(url, port),
        auth=auth,
        subdomain=subdomain,
        logger=logger,
    )
    session.state = ManagerSessionState.LOGIN
    return session
```


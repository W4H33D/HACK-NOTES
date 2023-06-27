# Access Control

## Controls

Before knowing about Access Controls let's first know about **controls**. In the context of cybersecurity and information technology, controls refer to the safeguards, measures, or countermeasures put in place to protect systems, data, and resources from unauthorized access, misuse, or damage. Controls are designed to mitigate risks, enforce security policies, and ensure information confidentiality, integrity, and availability.

## Access Controls Overview

Access Controls also refer to Authorization Control, In which we see who is authorized to perform the action and what level of access they have. On the access hand access control also refer to the mechanisms and policies implemented to regulate and manage access to resources, system, or data within an organization's information technology infrastructure. These Policies are referred to as the **Access Controls Models** and they are following.

### Access Controls Models

- **Programmatic Access Control**: Programmatic access control refers to the implementation of access control mechanisms within the code or logic of an application. It involves storing user privileges and related information in databases or other programmatic structures to control access to specific resources or functionalities.
- **Discretionary Access Control (DAC)**: In discretionary access controls, access to resources or functions is determined and enforced by the resource owners. The owners have the authority to assign or revoke access permissions for individual users or groups. This model provides a high level of control and allows owners to define access rights at a granular level.
- **Mandatory Access Control (MAC)**: In mandatory access controls, access to resources is determined by a central authority or security policy, and users or resource owners do not have the ability to assign or revoke access rights for their resources. The access control decisions are based on predefined rules or security labels associated with the resources and users.
- **Role-based Access Control (RBAC)**: In role-based access control, users are assigned specific roles that define their privileges and access rights within the system. A role represents a set of permissions associated with a particular job function or responsibility. Users can be assigned one or multiple roles depending on their responsibilities within the organization.
- **Physical Access Control**: In Physical Access Controls policies are made to secure physical spaces, such as data centers, server rooms, or restricted areas, through measures like locks, access cards, biometric authentication, CCTV surveillance, and security guards.
- **Logical Access Control**: In Logical Access Controls policies are implemented at the system or application level to restrict access to digital resources. They include mechanisms like user authentication (e.g., username/password, multi-factor authentication), authorization (defining user permissions and roles), and session management (managing active sessions and timeouts).
- **Network Access Control**: Network access controls regulate access to network resources and services. This can involve technologies like firewalls, network segmentation, virtual private networks (VPNs), and intrusion detection/prevention systems (IDS/IPS) to enforce network-level security policies.
- **Data Access Control**: Data access controls manage access to specific data or information assets. This can include encryption of sensitive data, data classification and labeling, data access permissions, and data loss prevention (DLP) measures.
- **Administrative Access Control**: Administrative access controls govern access to administrative or privileged functions within systems or applications. These controls limit administrative privileges to authorized personnel and ensure proper oversight and accountability for system administration activities.

In a web application, the access control is dependent on the user's login and session management process such as:

- **Authentication**: The process of verifying the identity of users attempting to access the application. Users are required to provide credentials, such as a username and password, which are then validated against a stored user database or an external authentication service.
- **Authorization**: Once a user is authenticated, authorization determines what actions or resources the user is allowed to access within the application. It involves defining user roles, permissions, and access levels, which are typically managed through access control lists (ACLs) or role-based access control (RBAC).
- **Session Management**: After successful authentication, a session is established between the user and the application. Session management controls the duration and state of the session, ensuring that users remain authenticated during their interaction with the application and preventing unauthorized access or session hijacking.

You may be asked if there **Is any difference in Authentication and Authorization?**

The answer is yes, They totally different things authentication is the first process in which they provide their credentials, and after that second process is Authorization in which they know what type of permissions they have and what type of things they are allowed to do in the application.

To manage the Authorization process we have configured the best access controls that regulate and manage user access to various resources and functionalities. If the application couldn't able to do so then serious vulnerabilities can occur. Designing and Managing access controls is a complex and dynamic problem and if that didn't address correctly then it affects the business, organizations, and legal constraints.

By knowing the importance of correct Access Controls they can be divided into the following categories.

- **Vertical Access Controls**
- **Horizontal Access Controls**
- **Context-dependent Access Controls**

## Vertical Access Controls

Vertical access controls restrict certain functions or actions to specific types of users. Different user types have different levels of access to perform certain tasks. For example, administrators may have more privileges, such as the ability to modify or delete user accounts, while regular users have limited access. Vertical access controls help enforce business policies by ensuring that only authorized users can perform certain actions or access sensitive functionality.

## Horizontal Access Controls

Horizontal access controls are implemented for lower privileged accounts or users who have access to less critical resources. Unlike vertical access controls that are often associated with higher privileged accounts, horizontal controls are typically tied to individual users and give them the ability to control access to their own files, folders, and resources. Users can determine who can edit, delete, or access their data. Horizontal access controls empower individual users to manage access to their specific resources and provide them with control over their own data.

## Context-dependent access controls

Context-dependent access controls restrict access to functionality and resources based on the specific state or context of the application or the user's interaction with it.

Context-dependent access controls ensure that users perform actions in the correct order or within specific constraints. For example, a retail website may prevent users from modifying the contents of their shopping cart after they have made a payment. This access control prevents users from unintentionally or maliciously manipulating their order after completing the transaction. By considering the context of the user's interaction (i.e., the completed payment), the system restricts access to certain functions to maintain data integrity and prevent unauthorized actions.


These are some common access controls that we use more frequently but there are some more access controls that are used in real life such as:

- Referer-based access control
- Location-based access control

I encourage the reader to do their own research on this topic to master this topic further. 

## Broken Access Controls

Now we get the overview of Access Controls, let's learn about access control issues that come to life when they are broken.

- **Broken Vertical Access Control**: If a user can gain access to functionality that they are not permitted to access then this is vertical privilege escalation. For example, if a non-administrative user can in fact gain access to an admin page where they can delete user accounts, then this is vertical privilege escalation.
- **Broken Horizontal Access Control**: If a user is able to access or modify resources that they should not have permission to, it indicates a broken horizontal access control. Broken horizontal access control refers to situations where a user can bypass the intended restrictions set by the resource owner, granting them unauthorized access to resources that are meant to be protected. We called that Horizontal Privilege Escalation.
- **Broken Context-Dependent Access Control**: Broken context-dependent access control occurs when the system fails to properly enforce access restrictions based on the specific context or conditions in which actions are performed. It refers to situations where the application allows unauthorized actions or operations due to inadequate consideration of the relevant context or conditions.

## Prevention

Access control vulnerabilities can generally be prevented by taking a defense-in-depth approach and applying the following principles:

- Never rely on obfuscation alone for access control.
- Unless a resource is intended to be publicly accessible, deny access by default.
- Wherever possible, use a single application-wide mechanism for enforcing access controls.
- At the code level, make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
- Thoroughly audit and test access controls to ensure they are working as designed.

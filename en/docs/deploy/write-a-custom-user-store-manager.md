# Write a Custom Userstore Manager

The following sections provide information that you need to be aware of
when writing a custom userstore manager.

---

## AbstractUserStoreManager and implementations

There are a set of methods available in the
`           AbstractUserStoreManager          ` class. These methods are
used when interacting with userstores. When we implement a custom userstore manager, it is important to identify the methods that must be
implemented or overridden.

!!! tip "Overriding methods"
    
    You must select the methods to override based on your requirement. For
    example, if you want to change the way you encrypt the password, you
    only need to implement the `           preparePassword          `
    method. If you want to implement a completely new read/write userstore
    manager, you need to implement all the methods listed in the tables given
    below. If the userstore is read-only, you can implement only the
    important methods and read methods (if you extend from
    `           AbstractUserStoreManager          `, you have to keep
    the unrelated methods empty).
    
    There are a few other methods used for internal purposes. You do not
    need to override those methods.
    

The following list briefly explains the use of the available methods in
the `           AbstractUserStoreManager          ` class. Most of the
methods provide a configuration option through properties. It is
recommended to use these methods with the provided customization
options.

### Important methods

<table>
<thead>
<tr class="header">
<th>Available methods</th>
<th>Default behavior</th>
<th>Reasons for overriding</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><code>                boolean doAuthenticate(String userName, Object credential)               </code></p></td>
<td>This method returns details on whether the given username and password is matched or not. Credential is usually a String literal.</td>
<td><p>If you want to change the authentication logic, you can override this method and write your own implementation. The default task of this method is to compare the given password with the stored password. The given credentials are passed to the <code>                preparePassword               </code> method to do the salting or encryption before the comparison takes place.</p></td>
</tr>
<tr class="even">
<td><p><code>                String preparePassword(Object password, String saltValue)               </code></p></td>
<td>This returns the encrypted or plain-text password based on the configurations.</td>
<td><p>You can override this method if you need to change the way you encrypt the password. If you want to change the algorithm that is used for encryption, you can configure it.</p></td>
</tr>
<tr class="odd">
<td><p><code>                Properties getDefaultUserStoreProperties()               </code></p></td>
<td><div class="content-wrapper">
<p>The default properties of the userstore are returned using this method. These properties are used in userstore related operations.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
    <p>Be sure to manually add the following property when you implement the class:</p>
    <div class="code panel pdl" style="border-width: 1px;">
    <div class="codeContent panelContent pdl">
    <div class="sourceCode" id="cb1" data-syntaxhighlighter-params="brush: java; gutter: false; theme: Confluence" data-theme="Confluence" style="brush: java; gutter: false; theme: Confluence"><pre class="sourceCode java"><code class="sourceCode java"><a class="sourceLine" id="cb1-1" title="1"><span class="fu">setOptionalProperty</span>(<span class="st">&quot;Disabled&quot;</span>, <span class="st">&quot;false&quot;</span>, <span class="st">&quot;Whether userstore is disabled&quot;</span>);</a></code></pre></div>
    </div>
    </div>
    <p>This property is what controls whether the userstore is enabled or disabled.</p></div>
</div></td>
<td><p>By overriding this method, you can programmatically change the configuration of the userstore manager implementation.</p></td>
</tr>
<tr class="even">
<td><p><code>                boolean checkUserNameValid(String userName)               </code></p></td>
<td><p>Returns whether the given username is compatible with the defined criteria</p></td>
<td><p>This is the criteria used for defining a valid username can be configured as a regex in userstore configurations. If you want to change the way user name validation is done, override this method.</p></td>
</tr>
<tr class="odd">
<td><p><code>                boolean checkUserPasswordValid(Object credential)               </code></p></td>
<td><p>This returns whether the given password is compatible with the defined criteria. This is invoked when creating a user, updating a password, and authorization.</p></td>
<td><p>Similar to the username, you can configure the format of a valid password in configuration. If you want to change that behavior, you can override this method.</p></td>
</tr>
</tbody>
</table>

### Read-write methods

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th>Available methods</th>
<th>Default behavior</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><code>                void doAddUser(String userName, Object credential, String[] roleList, Map&lt;String, String&gt; claims, String profileName, boolean requirePasswordChange)               </code></p></td>
<td><p>This method is responsible to create a new user based on the given values. We can change the JDBC query or LDAP attribute name with the userstore configuration.</p></td>
</tr>
<tr class="even">
<td><p><code>                void doDeleteUser(String userName)               </code></p></td>
<td><p>This removes the userstore record related to the given username.</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doUpdateCredential(String userName, Object newCredential, Object oldCredential)               </code></p></td>
<td><p>This is responsible to update the credential of the given username after authenticating with the existing password.</p></td>
</tr>
<tr class="even">
<td><p><code>                void doUpdateCredentialByAdmin(String userName, Object newCredential)               </code></p></td>
<td><p>Admin users can use this method to update the credentials of a given user. This can be done without validating the existing password.</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doAddRole(String roleName, String[] userList, boolean shared)               </code></p></td>
<td><p>This creates a new user role with given roleName and maps the given users to the newly created role. Shared parameter indicate whether this role is shared among tenant or not.</p></td>
</tr>
<tr class="even">
<td><p><code>                void doDeleteRole(String roleName)               </code></p></td>
<td><p>This method removes the given role and related mappings from the userstore.</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doUpdateRoleName(String roleName, String newRoleName)               </code></p></td>
<td><p>This method is used to update the name of the existing roles.</p></td>
</tr>
<tr class="even">
<td><p><code>                void doUpdateRoleListOfUser(String userName, String[] deletedRoles, String[] newRoles)               </code></p></td>
<td><p>This is used to delete the existing mappings between the given user and <code>                deletedRoles               </code> while creating mappings to <code>                newRoles               </code> .</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doUpdateUserListOfRole(String roleName, String[] deletedUsers, String[] newUsers)               </code></p></td>
<td><p>This is used to delete the existing mappings between the given role and  <code>                deletedUsers               </code> while creating mappings to <code>                newUsers               </code> .</p></td>
</tr>
<tr class="even">
<td><p><code>                void doSetUserClaimValue(String userName, String claimURI, String claimValue, String profileName)               </code></p></td>
<td><p>This is responsible for creating a new claim for a given user and profile with the given claim URI and value.</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doSetUserClaimValues(String userName, Map&lt;String, String&gt; claims, String profileName)               </code></p></td>
<td><p>This is responsible for creating a new claim for a given user and profile with the given list of claim URIs and values.</p></td>
</tr>
<tr class="even">
<td><p><code>                void doDeleteUserClaimValue(String userName, String claimURI, String profileName)               </code></p></td>
<td><p>Removes the existing claim details mapped with the given user and profile</p></td>
</tr>
<tr class="odd">
<td><p><code>                void doDeleteUserClaimValues(String userName, String[] claims, String profileName)               </code></p></td>
<td><p>Removes the given list of claims from a given user</p></td>
</tr>
<tr class="even">
<td><p><code>                void addRememberMe(String userName, String token)               </code></p></td>
<td><p>This method is used to persist tokens in the userstore.</p></td>
</tr>
</tbody>
</table>

### Read methods

| Available methods                                                                                                                       | Default behavior                                                                                                                                                                          |
|-----------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `                boolean doCheckExistingUser(String userName)               `                                                           | Returns whether the provided `               userName              ` already exists in the userstore                                                                                     |
| `                boolean doCheckExistingRole(String roleName)               `                                                           | Returns whether the provided `roleName` already exists in the userstore                                                                                                                    |
| `                String[] doListUsers(String filter, int maxItemLimit)               `                                                  | This method returns a list of usernames that match with the given filter string.                                                                                                           |
| `                String[] doGetRoleNames(String filter, int maxItemLimit)               `                                               | Returns a list of role names that match with the given filter string                                                                                                                      |
| `                String[] doGetExternalRoleListOfUser(String userName, String filter)               `                                   | Returns a list of external role names of a given user that match with the given filter string                                                                                             |
| `                String[] doGetSharedRoleListOfUser(String userName, String tenantDomain, String filter)               `                | This method returns a list of shared role names of a given user that match with the given filter string.                                                                                   |
| `                Map<String, String> getUserPropertyValues(String userName, String[] propertyNames, String profileName)               ` | This method returns values for the given `               propertyNames              ` for a given `               userName              ` and `               profileName              ` . |
| `                String[] getUserListFromProperties(String property, String value, String profileName)               `                  | This returns a list of usernames that match with the given value of the given property and `               profileName              ` .                                                    |
| `                String[] doGetDisplayNamesForInternalRole(String[] userNames)               `                                          | Returns names to display in the UI for given usernames                                                                                                                                    |
| `                Date getPasswordExpirationTime(String userName)               `                                                        | This returns the password expiry date of a given user. The default value is null.                                                                                                               |
| `                int getUserId(String username)               `                                                                         | This method returns the identifier of a given username. Default value is 0.                                                                                                               |
| `               boolean doCheckIsUserInRole(String userName, String roleName)              `                                            | `               True              ` is returned if the given user is already mapped to the given role name.                                                                                |
| `               String[] getProfileNames(String userName)              `                                                                | Returns a list of profile names mapped with a given username                                                                                                                             |
| `                String[] doGetSharedRoleNames(String tenantDomain, String filter, int maxItemLimit)               `                    | This returns a list of role names that are associated with the given tenant domain and match with the filter.                                                                              |
| `                String[] doGetUserListOfRole(String roleName, String filter)               `                                           | This method returns a list of usernames that are mapped with the given rolename.                                                                                                           |
| `                String[] getAllProfileNames()               `                                                                          | All the profile names are returned including the default profile.                                                                                                                          |
| `                boolean isValidRememberMeToken(String userName, String token)               `                                          | This method is used to check if the given token exists for the given user.                                                                                                                 |
| `                boolean isMultipleProfilesAllowed()               `                                                                    | This returns whether this userstore is allowed to have multiple profiles per user. The default value is `               false              `.                                                  |
| `                boolean isBulkImportSupported()               `                                                                        | This method returns whether this userstore allows bulk transactions or not.                                                                                                               |

### Implementations

In WSO2 Identity Server, there are four userstore manager classes
that implement the `           AbstractUserStoreManager          `
class. You can select one of those classes according to the userstore
that you have in your environment.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th>userstore manager class</th>
<th>When you would use it</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>               org.wso2.carbon.user.core.jdbc.UniqueIDJDBCUserStoreManager              </code></td>
<td><p>If your user details are stored in a database, you must use this userstore manager implementation. With the abstraction provided in this implementation, most of the JDBC based scenarios can be handled without writing a custom userstore manager.</p></td>
</tr>
<tr class="even">
<td><code>               org.wso2.carbon.user.core.ldap.UniqueIDReadOnlyLDAPUserStoreManager              </code></td>
<td><p>You can use this class if you have an LDAP userstore. This implementation does not allow you to insert or update users from the WSO2 Identity Server side. Instead, you can only read and use them in the product.</p></td>
</tr>
<tr class="odd">
<td><code>               org.wso2.carbon.user.core.ldap.UniqueIDReadWriteLDAPUserStoreManager              </code></td>
<td><p>If you want to allow WSO2 Identity Server to manipulate userstore data, you need to use this implementation.</p></td>
</tr>
<tr class="even">
<td><code>               org.wso2.carbon.user.core.ldap.UniqueIDActiveDirectoryLDAPUserStoreManager              </code></td>
<td><p>Active Directory also can be used as the userstore of WSO2 Identity Server and you can configure it using this userstore manager implementation.</p></td>
</tr>
</tbody>
</table>

---

## Implement a custom JDBC userstore manager

The instructions in this section are focused on implementing a sample
JDBC userstore manager. For this sample, the following tools are used
to implement the custom userstore manager.

-   Java 1.6.0
-   IDE (Eclipse, InteliJ IDEA, etc.)
-   Apache Maven

---

## Set up the implementation

To set up this implementation, follow the instructions given below.

1.  Create a new Apache Maven project with the help of the IDE that you
    are using. The project should be a simple Apache Maven project and
    you can use any desired artifact and group ID.
2.  Add the WSO2 userstore management .jar file as a dependency of our
    project. Since this .jar file is stored in WSO2's Maven repository,
    you need to add the WSO2 repository to your POM file. Given below is a sample POM file.

    !!! note
    
        The version number of the carbon dependencies
        given below have to be updated according to the carbon kernel version
        that the particular product version is compatible with. 
    

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>org.wso2.sample</groupId>
        <artifactId>CustomReadOnlyJDBCUserStoreManager</artifactId>
        <version>1.0.0</version>
        <packaging>bundle</packaging>
        <repositories>
            <repository>
                <id>wso2-nexus</id>
                <name>WSO2 internal Repository</name>
                <url>http://maven.wso2.org/nexus/content/groups/wso2-public/</url>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>daily</updatePolicy>
                    <checksumPolicy>ignore</checksumPolicy>
                </releases>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>org.wso2.carbon</groupId>
                <artifactId>org.wso2.carbon.user.core</artifactId>
                <version>4.4.11</version>
            </dependency>
            <dependency>
                <groupId>org.wso2.carbon</groupId>
                <artifactId>org.wso2.carbon.utils</artifactId>
                <version>4.4.11</version>
            </dependency>
            <dependency>
                <groupId>org.wso2.carbon</groupId>
                <artifactId>org.wso2.carbon.user.api</artifactId>
                <version>4.4.11</version>
            </dependency>
        </dependencies>

        <build>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.3.1</version>
                    <inherited>true</inherited>
                    <configuration>
                        <encoding>UTF-8</encoding>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.apache.felix</groupId>
                    <artifactId>maven-scr-plugin</artifactId>
                    <version>1.7.2</version>
                    <executions>
                        <execution>
                            <id>generate-scr-scrdescriptor</id>
                            <goals>
                                <goal>scr</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>org.apache.felix</groupId>
                    <artifactId>maven-bundle-plugin</artifactId>
                    <version>2.3.5</version>
                    <extensions>true</extensions>
                    <configuration>
                        <instructions>
                            <Bundle-SymbolicName>${project.artifactId}</Bundle-SymbolicName>
                            <Bundle-Name>${project.artifactId}</Bundle-Name>
                            <Private-Package>
                                org.wso2.sample.user.store.manager.internal
                            </Private-Package>
                            <Export-Package>
                                !org.wso2.sample.user.store.manager.internal,
                                org.wso2.sample.user.store.manager.*,
                            </Export-Package>
                            <Import-Package>
                                org.wso2.carbon.*,
                                org.apache.commons.logging.*,
                                org.osgi.framework.*,
                                org.osgi.service.component.*
                            </Import-Package>
                        </instructions>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

    Now your basic implementation is ready.

---

## Write a custom userstore manager for a sample scenario

Consider a sample scenario where you want
to use a custom hashing method using a 3rd party library such as
[Jasypt](http://www.jasypt.org/) and in order to do this, you need to
override the `           doAuthentication          ` and
`           preparePassword          ` methods.

Follow the steps given below to write a custom userstore manager.

1.  Include the required dependencies in your development environment.
    To do that, include the relevant Apache Maven dependency details or
    manually add the .jar files to your classpath. For example, add the
    following XML snippet under the dependencies tag in your pom.xml
    file to include the Jasypt dependency.

    ``` xml
    <dependency>
        <groupId>org.jasypt</groupId>
        <artifactId>jasypt</artifactId>
        <version>1.9.2</version>
    </dependency>
    ```

2.  Create a new class by extending the existing
    `             JDBCUserStoreManager            ` implementation. The
    following code is an example of how this would look.

    ``` java
    package com.wso2.custom.usermgt;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.jasypt.util.password.StrongPasswordEncryptor;
    import org.wso2.carbon.user.api.RealmConfiguration;
    import org.wso2.carbon.user.core.UserRealm;
    import org.wso2.carbon.user.core.UserStoreException;
    import org.wso2.carbon.user.core.claim.ClaimManager;
    import org.wso2.carbon.user.core.jdbc.JDBCUserStoreManager;
    import org.wso2.carbon.user.core.profile.ProfileConfigurationManager;

    import java.sql.Connection;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.sql.Timestamp;
    import java.util.Date;
    import java.util.GregorianCalendar;
    import java.util.Map;

    public class CustomUserStoreManager extends JDBCUserStoreManager {
        private static Log log = LogFactory.getLog(CustomUserStoreManager.class);
        // This instance is used to generate the hash values
        private static StrongPasswordEncryptor passwordEncryptor = new StrongPasswordEncryptor();

        // You must implement at least one constructor
        public CustomUserStoreManager(RealmConfiguration realmConfig, Map<String, Object> properties, ClaimManager
                claimManager, ProfileConfigurationManager profileManager, UserRealm realm, Integer tenantId)
                throws UserStoreException {
            super(realmConfig, properties, claimManager, profileManager, realm, tenantId);
            log.info("CustomUserStoreManager initialized...");
        }

        @Override
        public boolean doAuthenticate(String userName, Object credential) throws UserStoreException {
            boolean isAuthenticated = false;
            if (userName != null && credential != null) {
                try {
                    String candidatePassword = String.copyValueOf(((Secret) credential).getChars());

                    Connection dbConnection = null;
                    ResultSet rs = null;
                    PreparedStatement prepStmt = null;
                    String sql = null;
                    dbConnection = this.getDBConnection();
                    dbConnection.setAutoCommit(false);
                    // get the SQL statement used to select user details
                    sql = this.realmConfig.getUserStoreProperty("SelectUserSQL");
                    if (log.isDebugEnabled()) {
                        log.debug(sql);
                    }

                    prepStmt = dbConnection.prepareStatement(sql);
                    prepStmt.setString(1, userName);
                    // check whether tenant id is used
                    if (sql.contains("UM_TENANT_ID")) {
                        prepStmt.setInt(2, this.tenantId);
                    }

                    rs = prepStmt.executeQuery();
                    if (rs.next()) {
                        String storedPassword = rs.getString(3);

                        // check whether password is expired or not
                        boolean requireChange = rs.getBoolean(5);
                        Timestamp changedTime = rs.getTimestamp(6);
                        GregorianCalendar gc = new GregorianCalendar();
                        gc.add(GregorianCalendar.HOUR, -24);
                        Date date = gc.getTime();
                        if (!(requireChange && changedTime.before(date))) {
                            // compare the given password with stored password using jasypt
                            isAuthenticated = passwordEncryptor.checkPassword(candidatePassword, storedPassword);
                        }
                    }
                    dbConnection.commit();
                    log.info(userName + " is authenticated? " + isAuthenticated);
                } catch (SQLException exp) {
                    try {
                        connection.rollback();
                    } catch (SQLException e1) {
                        throw new UserStoreException("Transaction rollback connection error occurred while" + 
                            " retrieving user authentication info. Authentication Failure.", e1);
                    }
                    log.error("Error occurred while retrieving user authentication info.", exp);
                    throw new UserStoreException("Authentication Failure");
                }
            }
            return isAuthenticated;
        }

        @Override
        protected String preparePassword(Object password, String saltValue) throws UserStoreException {
            if (password != null) {
                String candidatePassword = String.copyValueOf(((Secret) password).getChars());
                // ignore saltValue for the time being
                log.info("Generating hash value using jasypt...");
                return passwordEncryptor.encryptPassword(password);
            } else {
                log.error("Password cannot be null");
                throw new UserStoreException("Authentication Failure");
            }
        }
    }
    ```

    !!! note
        The default constructor is not enough when you implement
        a custom userstore manager, and you must implement a constructor
        with relevant arguments.

---

## Deploy and configure the custom userstore manager

Follow the instructions given below to deploy and configure the custom userstore manager.

1.  Copy the artifact of your project (custom-userstore.jar, in this
    case) to the
    `           <IS_HOME>/repository/components/dropins          `
    directory. Also copy all the OSGI bundles to this location. If you have
    any dependency .jar files, copy them to the
    `           <IS_HOME>/repository/components/lib          `
    directory.

2.  Add the following configuration to the `<IS_HOME>/repository/conf/deployment.toml` file to use our custom implementation for userstore management.
    Although the existing `UniqueID` userstores are allowed by default, when adding a new userstore, note that both existing userstores as well as new userstores must be configured as shown below. 

    !!! abstract ""
        **Format**
        ```toml
        [user_store_mgt]
        allowed_user_stores=[<existing userstores..>,"<new userstore>"]
        ```
        ---
        **Sample**
        ```toml
        [user_store_mgt]
        allowed_user_stores=["org.wso2.carbon.user.core.jdbc.UniqueIDJDBCUserStoreManager", "org.wso2.carbon.user.core.ldap.UniqueIDActiveDirectoryUserStoreManager","org.wso2.carbon.user.core.ldap.UniqueIDReadOnlyLDAPUserStoreManager","org.wso2.carbon.user.core.ldap.UniqueIDReadWriteLDAPUserStoreManager","org.wso2.carbon.user.core.jdbc.JDBCUserStoreManager"]
        ```

    !!! tip
        This step provides instructions on configuring your custom userstore manager as a primary userstore. Alternatively, you can
        configure this as a secondary userstore if you already have a
        different primary userstore configured. For more information
        configuring userstores in WSO2 Identity Server, see [Configure Userstores](../../../deploy/configure-user-stores).
    

    You do not need to change anything else since you extend the
    `JDBCUserStoreManager` class, so the configurations will remain the
    same.

You have now implemented a custom userstore manager for WSO2 Identity Server.
Once you have done this, start the product and see the log messages that
you have placed inside overridden methods when you create a new user or
login. This ensures that all your configurations work as intended.

!!! note "Do you want to create a custom userstore that only has few enabled methods?"
    1.  Sign in to the WSO2 Identity Server management console (`https://<IS_HOST>:<PORT>/carbon`).
    2.  Click **Main** > **Userstores** > **Add**.
    3.  Select the custom userstore you just created as the value from the **Userstore Manager Class** dropdown.
    4.  Expand the **Advanced** tab and deselect the **Claim Operations Supported** property.
    5.  Click **Add**.
    
!!! info "Related topics"
    -   [Guide: Configure Userstores](../../../deploy/configure-user-stores)
    -   [Guide: Manage User Attributes](../../../guides/identity-lifecycles/manage-user-attributes#writing-custom-attributes)

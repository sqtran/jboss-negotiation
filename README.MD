# What is this?

This is a fork of the jboss-negotation project.  I wanted to fork it so that I could make modifications for testing purposes, and check it into a repository that I owned so that I could share it with my teammates.

Obviously, you need maven to compile/package this project.

You'll also need JBoss EAP or Wildfly to deploy it on.  Here's a sample standalone.xml file

```xml
<system-properties>
    <property name="jboss.security.disable.secdomain.option" value="false"/>
    <property name="java.security.krb5.debug" value="true"/>  <!-- Set to True if additional debugging is necessary -->
    <property name="java.security.krb5.conf" value="/path/to/krb5.conf"/>
</system-properties>

 <security-domain name="host" cache-type="default">
     <authentication>
         <login-module code="Kerberos" flag="required">
             <module-option name="storeKey" value="true"/>
             <module-option name="useKeyTab" value="true"/>
             <module-option name="refreshKrb5Config" value="true"/>
             <module-option name="principal" value="HTTP/ops-eap01.drax.od4apps.com@customer.com"/>
             <module-option name="keyTab" value="/path/to/file.keytab"/>
             <module-option name="doNotPrompt" value="true"/>
             <module-option name="debug" value="true"/>
             <module-option name="isInitiator" value="false"/>
         </login-module>
     </authentication>
 </security-domain>

<security-domain name="SPNEGO" cache-type="default">
    <authentication>
        <login-module code="SPNEGO" flag="requisite">
            <module-option name="password-stacking" value="useFirstPass"/>
            <module-option name="serverSecurityDomain" value="host"/>
            <module-option name="removeRealmFromPrincipal" value="true"/>
            <module-option name="usernamePasswordDomain" value="ad-domain"/>
        </login-module>
        <login-module code="LdapExtended" flag="required">
            <module-option name="password-stacking" value="useFirstPass"/>
            <module-option name="bindDN" value="XXXXXXXX"/>
            <module-option name="bindCredential" value="XXXXXXXX"/>
            <module-option name="java.naming.provider.url" value="ldap://corp.customer.com"/>
            <module-option name="baseCtxDN" value="DC=corp,DC=customer,DC=com"/>
            <module-option name="baseFilter" value="(sAMAccountName={0})"/>
            <module-option name="rolesCtxDN" value="DC=corp,DC=customer,DC=com"/>
            <module-option name="roleFilter" value="(sAMAccountName={0})"/>
            <module-option name="roleAttributeID" value="memberOf"/>
            <module-option name="roleAttributeIsDN" value="true"/>
            <module-option name="roleNameAttributeID" value="CN"/>
            <module-option name="java.naming.referral" value="follow"/>
        </login-module>
    </authentication>
    <mapping>
      <mapping-module code="org.jboss.security.mapping.providers.DeploymentRoleToRolesMappingProvider" type="role"/>
    </mapping>
</security-domain>
<security-domain name="ad-domain" cache-type="default">
    <authentication>
        <login-module code="LdapExtended" flag="required">
            <module-option name="password-stacking" value="useFirstPass"/>
            <module-option name="bindDN" value="XXXXXXXX"/>
            <module-option name="bindCredential" value="XXXXXXXX"/>
            <module-option name="java.naming.provider.url" value="ldap://corp.customer.com"/>
            <module-option name="baseCtxDN" value="DC=corp,DC=customer,DC=com"/>
            <module-option name="baseFilter" value="(sAMAccountName={0})"/>
        </login-module>
    </authentication>
</security-domain>
```

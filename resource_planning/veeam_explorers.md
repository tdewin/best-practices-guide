# Veeam Explorers

For recommendations on resource planning for Veeam Explorers – the tools
that support application item-level restore – please refer to the
corresponding [User
Guide](http://helpcenter.veeam.com/backup/80/explorers/index.html?introduction.html)
(in particular, to "Before You Begin" sections).

**Note:** Some recommendations for the staging system (if required for
Veeam Explorer tool) can be also found in the "Veeam Backup Server" and
"Veeam Backup & Replication Database" sections of this document.

If you have special features/enhancements/configuration settings on the
production Microsoft SQL and/or Microsoft SharePoint server to be
protected with Veeam, these custom settings should be implemented on the
staging SQL Server, too.

For Microsoft Active Directory, also check the tombstone lifetime
settings, as described in Veeam Explorers User Guide at Veeam Help
Center
(<http://helpcenter.veeam.com/backup/80/explorers/index.html?vead_recommendations.html>).

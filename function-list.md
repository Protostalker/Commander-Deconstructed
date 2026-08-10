Verifone Commander — Full Function List (365 commands, MANAGER account)
Sorted alphabetically. Format: CMD                                    DESCRIPTION
Retrieved: 2026-08-10 from live unit via NAXML validate response funcList

PREFIX KEY:
  v = View/Read (safe, no side effects)
  u = Update/Write (modifies Commander DB, not yet live)
  c = Commit/Control (pushes to dispensers or triggers an action)
  d = Delete

========================================================================

allowAllCsrRpts                     Allow all cashier reports view
allowOpenCsrRpts                    Allow open cashier reports view
bypassEmployeeId                    Bypass employee id validation
cbeginupgrade                       Request Auto Upgrade Engine to begin upgrade
ccarwashdisable                     Disable Car Wash
ccarwashenable                      Enable Car Wash
cclosedaynow                        Close Day Now
cclosepdnow                         Close Period Now
ccloudconnect                       Register Commander with Verifone Cloud
ccwpaypointinit                     Initialize Carwash Paypoint
ccwpdclose                          Carwash Paypoint Period Close
cdcrdriverinit                      Initialize DCR Driver
cdcrinit                            Initialize DCR
cfdcposrequest                      Process POS to FDC request
cfeatureenablement                  Update a feature
cforcesynccdm                       Performs force sync of CM
cFPDinit                            Init FP Display Config
cfueldrvinit                        Initialize Fuel Driver
cfuelinit                           Initialize Fuel
cfuelprices                         Download Fuel Prices
cgeneratepopcodes                   Auto generates POP Codes.
changepasswd                        Change Password
cincrementdcrkey                    Increment
ckdsfunction                        Run KDS System Call Functions
cpairingsvc                         Start Pairing Listener service
cping                               Pings a given destination.
cprobecdmagenthost                  Test accessibility to the cdm host url
cprobecmdrcnslhost                  Test accessibility to the commander console url
crcfinit                            Init Rapid Change Fuel Config
creboot                             Reboot Commander
crefreshcfg                         Refresh Configuration
crefreshepsconfig                   Refresh Eps Config
cregisterageservice                 Register with remote Age verification service
cregisterdcr                        Register
cregmanageddevice                   Register the Device
cregsysupdates                      Register for System Updates
creinitsshca                        Re-initialize SSH CA
cswupgradepkg                       Update software
cunattendeddisable                  Disable Unattended Mode
cunattendedenable                   Enable Unattended Mode
cunattendedschedule                 Schedule Unattended Mode
cupdatedcrsettings                  Update DCR
notifyamber                         Notify amber alert updates
releaseCredential                   Release the credential
repeatEvent                         Repeat last event
sendPLUsToGempro                    Send plu.dat to GEMPRO
uageservice                         Update Age verification service config
uagevalidationcfg                   Update age validation config
uaptglobalcfg                       Update APT Global Config
uaptterminalcfg                     Update APT Terminal Config
ubannercfg                          Update banner config
ubluelawcfg                         Update blue law config
ucarwashcfg                         Update carwash config
ucashaccsite                        Update cash acceptor config
ucashierenddraweramts               Update Cashier End Drawer Amounts
ucashierreportreviewstatus          Update Cashier Report Review Status
ucashiertrackingcfg                 Update cashier tracking configuration
ucashrecyclercfg                    Update Cash Recycler Configuration
ucategorycfg                        Update category config
ucdmagentprop                       Update CDM cloud agent properties
uchallengequestions                 Update the challenge questions for a user
ucharitycfg                         Update Charity Configuration
uchecklinecfg                       Update check franking config
ucloselanecfg                       Update Close Lane Configuration
ucloudagentprop                     Update commander console cloud agent properties
ucouponfamcfg                       Update coupon family config
ucurrencycfg                        Update currency config
ucwpaypointcfg                      Update carwash paypoint config
udatetime                           Set Time and Date
udcrheadercfg                       Update DCR header config
udcridlescreencfg                   Update DCR Idle Screen config
udcrmessagecfg                      Update DCR message config
udcrtrailercfg                      Update DCR trailer config
udcrunattendedcfg                   Update Unattended configuration
udepartmentcfg                      Update department config
udiscountdenomcfg                   Update discount denom config
uecheckcfg                          Update Echeck Config
uemployeecfg                        Update Employee config
uempprefcfg                         Update Cashier Preferences Configuration
uemvcfg                             Update EMV Configuration
uemvinit                            Update EMV Initialization
uepsprepaidcfg                      Update EPS Prepaid Card Config
uesafecfg                           Update Esafe Config
ueventcfg                           Update event configuration
ufeecfg                             Update fee config
ufepcardcfg                         Update Fep's card configuration parameters
ufepcardtypecfg                     Update Fep's card configuration parameters based on card Type
ufepcfg                             Update Fep's configuration parameters
ufoodservicecfg                     Update Food Service Config
ufoodserviceplumapcfg               Update Mobile Food Order PLU Mapping Configuration
uFPDcfg                             Update FP Display Config
ufuelcfg                            Update Fuel Site Configuration
ufuelprices                         Update Fuel Prices
ufueltaxex                          Update Fuel Tax Exemption Config
ufueltaxexemptcfg                   Update Fuel Tax Exemption Config
ufueltaxexemptreceiptcfg            Update Fuel Tax Exemption Receipt Config
ufunctionlist                       Refresh function list
ugrouplist                          Update Group List Configuration
uifsfcfg                            Update IFSF Network Config
uimagecfg                           Update Image Config
uinhouseacctcfg                     Update in-house account config
ukioskorder                         Commit kiosk order
ulogocfg                            Update logo config
uloyaltycardcfg                     Update Loyalty Card Configuration
uloyaltycardtypecfg                 Update Loyalty Card Configuration
uloyaltyglobalcfg                   Update Global Loyalty parameters
uloyaltymobilevascfg                Update Mobile VAS Loyalty Config
uMaintenance                        Update NAXML maintenance dataset
umaintfprht                         Update maint fprht
umaintpostal                        Update maint postal
umaintregistrationkey               Update maint registration key
umainttelephone                     Update maint telephone
umainttotalizers                    Update maint totalizers
umanagedcfg                         Update Managed Configuration
umanageddevicecfg                   Update Manage Devices configuration
umanagedmodulecfg                   Update Configuration
umanageradjustment                  Update Manager Adjustments
umanagercorrection                  Update Manager Corrections
umenucfg                            Update menu config
umobilecfg                          Update Mobile Configuration
umopcfg                             Update MOP config
umwscashmovementreport              Update MWS Cash Movement Info
unetposcfg                          Update Network Configuration
unetworkcfg                         Update Network Settings
unetworkpartcfg                     Update network settings
upanelcfg                           Configure panels
upaymentcfg                         Update payment config
upinpadmsgcfg                       Update Pinpad Idle and Swipe messages
uplupromocfg                        Update PLU Promo config
uPLUs                               Update PLU dataset
upolicycfg                          Update policy config
upopcfg                             Update pop config
uposcfg                             Update POS config
upospaymentconfig                   Update POS Payment Configuration
uposscreencfg                       Configure Register specific touch screens
upossecurity                        Update POS security config
upscdcrcfg                          Update DCR config
upscdcrkeycfg                       Update DCR Key config
uptpecfg                            Update PTPE Configuration
uquickchipcfg                       Update Quick Chip Configuration
urcfcfg                             Update Rapid Change Fuel Config
uregistercfg                        Update register config
ureportcfg                          Update Report Configuration
ureportlist                         Update Report List Configuration
ureportstatus                       Update Manager Review Status
urestrictionscfg                    Update restriction config
uroleadmin                          Update Role configuration
usalescfg                           Update sales config
uscocategorycfg                     Update Self-checkout Category Configuration
uscoglobalcfg                       Update Self-checkout Global Configuration
uscoregistercfg                     Update Self-checkout Register Configuration
uscreencfg                          Import touch screen Configuration from B52 or older
uscreencfgv2                        Configure reusable screens
usecurityctrlcfg                    Update Security Control config
uslogancfg                          Update slogan config
usoftkeycfg                         Update softkey config
usoftkeytypesecuritycfg             Update Softkey Type Security Config
usupportinfo                        Update support information
utaxratecfg                         Update tax rate config
uthirdpartyproductprovidercfg       Update Third Party Product Provider Configuration
utlssite                            Update TLS config
utriggerpullcfg                     Update Trigger Pull Configuration
uuseradmin                          User Administration
uvendingmachinecfg                  Update Vending Machine config
uvhqcfg                             Update VHQ Configuration
uvipercfg                           Update Viper's site level Config
uvistagroupcfg                      Update Vista Device Group Config
uvistaitemsetcfg                    Update Vista Device Itemset Config
uvistaitemsubsetcfg                 Update Vista Device Item Subset Config
uvistaterminalcfg                   Update Vista Device Terminal Config
uvistaterminalpreviewcfg            Update Vista Device Terminal Preview Config
validate                            Login — returns session token in <cookie>
vageservice                         View Age verification service config
vagevalidationcfg                   View age validation config
vappcfg                             View App Specific Config
vAppInfo                            View App Info
vappmodules                         View App Module Names
vaptglobalcfg                       View APT Global Config
vaptterminalcfg                     View APT Terminal Config
vattendantpdlist                    View attendant report period list
vattendantrept                      Attendant Reports
vbannercfg                          View banner config
vbluelawcfg                         View blue law config
vcarwashcfg                         View carwash config
vcashaccsite                        View cash acceptor config
vcashierpdlist                      View cashier report period list
vcashierrept                        Cashier Reports
vcashiertrackingcfg                 View cashier tracking configuration
vcashiertrackingrept                View cashier tracking report
vcashrecyclercfg                    View Cash Recycler Configuration
vcategorycfg                        View category config
vcdmagentprop                       View CDM agent properties
vchallengequestions                 View all the allowed challenge questions for a user to set
vcharitycfg                         View Charity Configuration
vchecklinecfg                       View check franking config
vcloselanecfg                       View Close Lane Configuration
vcloudagentprop                     View commander console cloud agent properties
vcloudconnectstatus                 View registration status of Commander with Verifone Cloud
vcouponfamcfg                       View coupon family config
vcurrencycfg                        View currency config
vcwpaypointcfg                      View carwash paypoint config
vcwpaypointpdlist                   View cw paypoint period list
vcwpaypointpdrept                   View cw paypoint period report
vdatetime                           Get Time and Date
vdcrheadercfg                       View DCR header config
vdcridlescreencfg                   View DCR Idle Screen config
vdcrmessagecfg                      View DCR message config
vdcrtrailercfg                      View DCR trailer config
vdcrunattendedcfg                   View Unattended configuration
vdepartmentcfg                      View department config
vdiscountdenomcfg                   View discount denom config
vecheckcfg                          View Echeck Config
vemployeecfg                        View Employee config
vempprefcfg                         View Cashier Preferences Configuration
vemvcfg                             View EMV Configuration
vemvinit                            View EMV Initialization
vepsprepaidcfg                      View EPS Prepaid Card Config
vepssiteassetdata                   View Site Asset Data of EPS
vesafecashierrept                   ESafe Cashier Reports
vesafecfg                           View Esafe Config
veventcfg                           View event configuration
veventhistory                       View Event History
veventset                           Register event listener
veventunset                         Unregister event listener
vfeaturelist                        View feature list
vfeecfg                             View fee config
vfepcardcfg                         View Fep's card configuration parameters
vfepcardtypecfg                     View Fep's card configuration parameters based on card Type
vfepcfg                             View Fep's configuration parameters
vfepdetails                         View Basic Fep Details
vfoodservicecfg                     View Food Service Config
vfoodservicedept                    View Food Service Departments
vfoodserviceplumapcfg               View Food Service PLU Mapping configuration
vforecourtdiagnostics               View Diagnostics info of Forecourt
vFPDcfg                             View FP Display Config
vfuelcfg                            Fuel Site Configuration
vfuelprices                         View Fuel Prices
vfuelrtcfg                          In-effect Fuel Site Configuration
vfuelrtprices                       View In-effect Fuel Prices
vfueltaxex                          View Fuel Tax Exemption Config
vfueltaxexemptcfg                   View Fuel Tax Exemption Config
vfueltaxexemptreceiptcfg            View Fuel Tax Exemption Receipt Config
vfueltotals                         Fuel Totals Report
vfueltotalsz                        Compressed Fuel Totals Report
vfuturemobfoodordrpt                View Future Mobile Food Order Report
vgrouplist                          View Group List Configuration
vhostedmanagers                     View Module framework hosts
vifsfcfg                            View IFSF Network Config
vimagecfg                           View Image Config
vinhouseacctcfg                     View in-house account config
vkioskupgradesummary                View Kiosk Upgrade Summary Report
vlogocfg                            View logo config
vloyaltycardcfg                     View Loyalty Card Configuration
vloyaltycardtypecfg                 View Loyalty Card Configuration
vloyaltyglobalcfg                   View Global Loyalty parameters
vloyaltymobilevascfg                View Mobile VAS Loyalty Config
vMaintenance                        View NAXML maintenance datasets
vmaintfprht                         View maint fueling point running hose totals
vmaintpostal                        View maint postal code
vmaintregistration                  View maint registration
vmainttelephone                     View maint telephone number
vmainttotalizers                    View maint totalizers
vmanagedcfgstatus                   View Managed Update Status
vmanageddevicecfg                   View Manage Devices configuration
vmanagedmodulecfg                   View Configuration
vmenucfg                            View menu config
vmnspdiagnostics                    Get MSNP VPN connection diagnostic information
vmobfoodordsitecfg                  View Mobile Food Order Site Configuration
vmobileasloyaltyreportlist          View Mobile Above Site Loyalty Report List
vmobilecfg                          View Mobile Configuration
vmobilediagnostics                  View Mobile Diagnostics
vmobilehostlist                     View Hosts List
vmobilereport                       View Mobile Report
vmobilereportcategorylist           View Reports Category List
vmobilereportlist                   View Reports List
vmoddescmap                         View Module/Descriptions map
vmodulecfg                          View a Module Configuration
vmodulecfgref                       View Module Referentials
vmopcfg                             View MOP config
vMovement                           View NAXML movement reports
vmwscashierdraweramts               View Cashier Drawer Amounts
vmwscashmovementreport              View MWS Cash Movement Report
vmwslog                             Manager workstation event logs
vmwslogpdlist                       View Manager Accepted Reports
vmwsposjournal                      View manager work station event in POSJournal reports
vnetposcfg                          View Network Configuration
vnetworkcfg                         View Network Settings
vnetworkmenu                        View Network Menu xml
voutdoortavecfg                     View Outdoor TAVE config
vpairingmgt                         POS Pairing service management
vpairingotp                         Retrieve current pairing OTP
vpairingsvc                         Is Pairing Listener service started
vpanelcfg                           View panels
vpaymentcfg                         View payment config
vpaymentdiagnostics                 View Payments Diagnostics
vpayrollpdlist                      View Ruby payroll period list
vpayrollpdlist2                     View payroll period list
vpayrollrept                        Payroll Reports
vpayrollrept2                       Payroll Reports (new format)
vpendmdlcfg                         View Pending Configurations
vperiodlist                         View period list
vpinpadmsgcfg                       View Pinpad Idle and Swipe messages
vplupromocfg                        View PLU Promo config
vPLUs                               View PLU dataset
vPLUUpdateStatus                    View PLU Update status
vpolicycfg                          View policy config
vpopcfg                             View pop config
vposcfg                             View POS config
vposdiagnostics                     View POS Diagnostics info
vposjournal                         View NAXML POSJournal reports
vpospaymentconfig                   View POS Payment Configuration
vposscreencfg                       View Register specific touch screens
vpossecurity                        View POS security config
vprodcodecfg                        View product code config
vproprietarynetworkmenu             View Proprietary Network Menu xml
vpscdcrcfg                          View DCR config
vpscdcrkeycfg                       View DCR Key config
vptpecfg                            View PTPE Configuration
vquickchipcfg                       View Quick Chip Configuration
vrcfcfg                             View Rapid Change Fuel Config
vrefinteg                           View support doc
vregistercfg                        View register config
vreportcfg                          View Report Configuration
vreportlist                         View Report List Configuration
vreportpdlist                       View report period list
vreportstatus                       View Manager Review Status
vrestrictionscfg                    View restriction config
vroleadmin                          View Role configuration
vroutingcategories                  View Food Service Routing Categories
vrubyrept                           Ruby Reports
vsalescfg                           View sales config
vsalesnetworkmenu                   View Sales Network Menu xml
vscocategorycfg                     View Self-checkout Category Configuration
vscoglobalcfg                       View Self-checkout Global Configuration
vscoregistercfg                     View Self-checkout Register Configuration
vscreencfgv2                        View reusable screens
vsecurityctrlcfg                    View Security Control config
vsiteassetdata                      View Site Asset Data
vslogancfg                          View slogan config
vsoftkeycfg                         View softkey config
vsoftkeytypesecuritycfg             View Softkey Type Security Config
vsupportinfo                        View support information
vsysresourcesmap                    View System Resource Mappings
vtaxratecfg                         View tax rate config
vthemecfg                           View Screen Themes
vthirdpartyproductprovidercfg       View Third Party Product Provider Configuration
vtilleventreport                    View Till Event Reports
vtlogpdlist                         View T-Log period list
vtlssite                            View TLS config
vtransdetails                       View complete details of a given transaction
vtransnumlist                       View all transaction numbers
vtransset                           Period Reports with fully masked card holder data
vtranssetz                          Compressed Period Reports with fully masked card holder data
vtriggerpullcfg                     View Trigger Pull Configuration
vupgradesummary                     View Upgrade Summary Report
vuseradmin                          View User configuration
vvendingmachinecfg                  View Vending Machine config
vvhqcfg                             View VHQ Configuration
vvipercfg                           View Viper's site level Config
vviperpdlist                        Allow Viper period list view
vviperrept                          Viper reports
vvistagroupcfg                      View Vista Device Group Config
vvistaitemsetcfg                    View Vista Device Itemset Config
vvistaitemsubsetcfg                 View Vista Device Item Subset Config
vvistaterminalcfg                   View Vista Device Terminal Config
vvistaterminalpreviewcfg            View Vista Device Terminal Preview Config

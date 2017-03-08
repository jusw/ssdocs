SpreadServe Licensing
=====================

**Dual license model**

SpreadServe is available under two kinds of license: public and offline.

* Public

  * Free: there is no charge for running a public SpreadServe instance as an AMI or as installed via the install kit.
  * Phone home: public SpreadServe instances phone home to spreadserve.com. The SpreadServeEngine sends three kinds of
    messages back to spreadserve.com
    
    * Handshake: on startup each SpreadServeEngine handshakes with spreadserve.com. If the handshake fails it will shutdown.
    * Ping: each SpreadServeEngine pings spreadserve.com every 30 seconds. That's why the Live Engines panel on the 
      spreadserve.com dashboard flashes every 30 seconds.
    * Load: every time a SpreadServeEngine loads a spreadsheet it phones home with the name of the spreadsheet and all
      the formulae in the sheet. All that information then becomes searchable for logged in users in the spreadserve.com
      dashboard.
      
  * Default: all install kits and AMIs include a public license key.
  
* Offline

  * Chargeable: there's a monthly fee for an offline license.
  * Silent: SpreadServe does not phone home to spreadserve.com when running with an offline license key.
  * Time bound: offline license keys can be bought in monthly increments for months and years ahead.
  * Host bound: offline license keys are locked to the MAC address of your host.
  * Tiered: the license charge scales with the feature set as you add XLL, VBA and RTD capabilities.

* Private

  * Chargeable: there's a monthly fee for n private license.
  * Quiet: SpreadServe does phone home to spreadserve.com when running with an offline license key, but
    only for the handshake. There are no pings, and no load information is phoned home. Private hosts do
    not appear on spreadserve.com's dashboard.
  * Time bound: private license keys can be bought in monthly increments for months and years ahead.
  * Host flexibility: private license keys are not locked to the MAC address of your host. If you have 
    ten licenses, then you can run SpreadServe on any ten hosts.
  * Tiered: the license charge scales with the feature set as you add XLL, VBA and RTD capabilities.  
  
**License fee structure**

  * Choose one of three currencies: GBP, EUR or USD
  * Base
  
    * Standard Excel formula set. No XLL, VBA or RTD.
    * 100 per host per month.
    
  * XLL & VBA
  
    * Standard Excel formula set. VBA & XLL. No RTD.
    * 300 per host per month.
    
  * XLL, VBA & RTD
  
    * Complete feature set.
    * 400 per host per month.
    
  * 3pac: DEV, UAT, PRD
  
    * Three keys for the price of two.
    * Suitable for enterprises running development, UAT and production environments.
    
  * 5pac: DEV, SIT, UAT, STG, PRD
  
    * Five keys for the price of three.
    * Suitable for enterprises running development, integration, UAT, staging and production environments.
    
**Consultancy**

Bespoke development, configuration, and deployment services are provided on a day rate.

**Beta licensing**

Different license terms apply during SpreadServe's Cloud Beta.

  * Offline license keys are not chargeable. 
  * Tiered licensing doesn't apply: all beta licenses grant XLL, VBA & RTD.
  * Beta licenses are not host bound; they work on any server.
  * Private license keys are not available.

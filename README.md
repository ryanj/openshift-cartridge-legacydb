# OpenShift LegacyDB example cartridge

This OpenShift add-on cartridge is meant to be modified and reused by OpenShift operators in order to provide application configuration settings for accessing an existing network-hosted data service (external to OpenShift).

Installing your own modified Legacy service cart in an [OpenShift Enterprise](https://www.openshift.com/products/enterprise) or [OpenShift Origin](https://www.openshift.com/products/origin) deployment can help developers autoconfigure their hosted applications to take advantage of existing data services on your network.

## Clone and Modify this cart
First, make a local clone of this repo, and push another clone of the source to a private git repo (hosted inside your firewall).  If you include your service configuration strings in your modified cartridge source, then make sure to keep that source somewhere private.  A privately hosted git repo is ideal here, since it can be accessed by your OpenShift deployment.  You could also try modifying the `bin/setup` script in order to pull configurations strings from a configuration server, or from another resource within your private network.

Modify the files in `env` so that they include your service configuration strings.  Then, update `bin/setup`, and `bin/install` to match your changes.

For detailed instructions see the [OpenShift Cartridge developers guide](http://openshift.github.io/documentation/oo_cartridge_developers_guide.html).

Finally, update the Env var documentation in `metadata/manifest.yml` before adding and committing your changes.  Push your resulting modifications to your private git hosting solution (not to a public git repo, like GitHub).

## Test your changes
You can test your modified cartridge before adding it to the list of available cartridge services.

Try creating the following application using the `rhc` command line tool:

    rhc app create myapp python-2.7 http://yourgitserver.com/username/external-service-cart.git

You can verify that your configuration strings were successfully published into the system environment by connecting to your application using `ssh`:

    rhc ssh myapp 
    env | grep LEGACY

## Cartridge Installation
To add your modified cart to your global OpenShift cartridge list, run the following as an OpenShift administrator:

    oo-admin-ctl-cartridge -c import --url https://yourhostedgit.com/username/external-service-cart/master/metadata/manifest.yml --activate

This should allow users of your OpenShift deployment to see your external service solution as an available configuration option in the system cartridge listing:

    rhc cartridge list

It should also allow them to request your dataservice configuration strings as a formal cartridge dependency:

    rhc app create myapp python-2.7 legacydb-1.0

OpenShift includes additional tools for helping operators remove, unpublish, or upgrade their OpenShift cartridges:

    oo-admin-ctl-cartridge -c delete --ids legacydb-1.0

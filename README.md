# dafs Bosh Release

This repository contains a BOSH release for a dafs service.

## Setup and deploy

1. Setup bosh deployment environment

    a. Clone deployment configs

        $ git clone https://github.com/cloudfoundry/bosh-deployment bosh-deployment

    b. Create bosh environment

        $ mkdir vbox
        $ bosh create-env bosh-deployment/bosh.yml \
            --state vbox/state.json \
            -o bosh-deployment/virtualbox/cpi.yml \
            -o bosh-deployment/virtualbox/outbound-network.yml \
            -o bosh-deployment/bosh-lite.yml \
            -o bosh-deployment/bosh-lite-runc.yml \
            -o bosh-deployment/jumpbox-user.yml \
            --vars-store vbox/creds.yml \
            -v director_name="Bosh Lite Director" \
            -v internal_ip=192.168.50.6 \
            -v internal_gw=192.168.50.1 \
            -v internal_cidr=192.168.50.0/24 \
            -v outbound_network_name=NatNetwork

    b. Setup routing and add images

        $ sudo route add -net 10.244.0.0/16 192.168.50.6
        $ wget --content-disposition https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent
        $ bosh -e vbox upload-stemcell bosh-stemcell-*-warden-boshlite-ubuntu-trusty-go_agent.tgz

2. Deploy dafs in bosh

    a. Create release

        $ bosh create-release --force

    b. Deploy release

        $ bosh -e vbox upload-release
        $ bosh -e vbox -d dafs deploy manifest.yml

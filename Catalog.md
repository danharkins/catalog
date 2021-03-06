# Catalog of Onboarding Mechanisms for IoT Devices

When considering each of these technologies, provide one or two sentences on what problem each sets out to solve, a few sentences on how it solves that problem, and then talk about constraints.  Keep in mind our goal for seeking common architectural components.  Some leading questions to help:

 * How does device introduce itself to the infrastructure or peer?
 * What, if any, proof of possession mechanism is there?
 * Is access to other IP-based devices required in order to fully onboard the device?
 * What form of credential is installed on the device?
 * Is full Internet access required for onboarding?
 * Who becomes the root of trust at the end of onboarding (if any)
 * Could/Is the resulting credential be used for application identity?
 * What happens if the box gets reset?
 * How can transfer of ownership occur?
 * How can transfer to a different cloud service (if applicable) occur?
 * What sort of manufacturing requirements are there?
 * What sort of crypto requirements are there?
 * Reference link

Feel free to add or vary the questions.

## OCF WiFi Easy Setup

 * How does device introduce itself to the infrastructure or peer?

The device starts as a SoftAP, and so advertises WiFi beacons.

 * What, if any, proof of possession mechanism is there?

Physical proximity can be established using a random PIN based mechanism, where the PIN
is communicated over an out-of-band channel (e.g., displayed to a human).  See 
Section 7.3.5 of the 
[OCF Security Specification](https://openconnectivity.org/specs/OCF_Security_Specification_v1.3.0.pdf).

 * Is access to other IP-based devices required in order to fully onboard the device?

No.

 * What form of credential is returned?

Such information includes L2 credentials
(e.g., WiFi SSID and key), as well as the owner's cert, and optionally a cloud service
URI and credentials needed to sign into it.

 * Is full Internet access required for onboarding?

No, onboarding happens prior to getting IP connectivity.
Onboarding can thus be used to provision credentials needed to get network access.

 * Who becomes the root of trust at the end of onboarding (if any)

The new owner's certificate becomes the root of trust.

 * Could/Is the resulting credential be used for application identity?

The question is bad because "the" assumes that onboarding only provisions one thing.
Application identity can be provisioned along with L2 credentials, which may be different.

 * What happens if the box gets reset?

A factory reset will typically remove all credentials, leaving the device ready to be
onboarded again.

 * How can transfer of ownership occur?

There are multiple mechanisms specified in Section 7.3 of the 
[OCF Security Specification](https://openconnectivity.org/specs/OCF_Security_Specification_v1.3.0.pdf)).
In general, before the device is put onto the network, the new owner's certificate
is provisioned into the device so that by the time the device gets access to the
regular network, it is already fully configured with all security credentials.

 * How can transfer to a different cloud service (if applicable) occur?

Subsequent ownership transfer can happen either by doing a factory reset and starting from scratch,
or by following an ownership transfer mechanism that is not part of onboarding,
and hence outside the scope of this survey.

 * What sort of manufacturing requirements are there?

The manufacturer is responsible for adding code complying with the specification.
Optionally, the manufacturer can also provision a manufacturer's certificate
of device authenticity (see Section 7.3.6 of the 
[OCF Security Specification](https://openconnectivity.org/specs/OCF_Security_Specification_v1.3.0.pdf))

 * What sort of crypto requirements are there?

Several different mechanisms are defined, with varying levels of crypto requirements,
so that the WiFi Easy Setup can be used by a wide variety of devices.

 * Reference link

[OCF WiFi Easy Setup](https://openconnectivity.org/specs/OCF_Core_Specification_Extension_Wi-Fi_Easy_Setup_v1.3.0.pdf)

[OCF Security Specification](https://openconnectivity.org/specs/OCF_Security_Specification_v1.3.0.pdf)

## Device Provisioning Protocol (DPP)
 * How does device introduce itself to the infrastructure or peer?
 
There are multiple approaches for trusted introduction, but in the first instance, DPP makes use of a *configurator*, a device that is proximate to the device to be onboarded, and contacts the peer with an 802.11 action frame.

The Configurator is the nominal "owner" of the network. It has created the policy for the
network, and it is assumed that (in the non-legacy case) the Configurator has first provisioned
the Access Point (AP) using DPP. Once the AP is provisioned it establishes the DPP network
and from then on the Configurator does DPP with clients. 
 
 * What, if any, proof of possession mechanism is there?
 
A "bootstrapping method" is used to obtain trust in a device's public key.
Different bootstrapping methods provided different levels of trust. Defined
bootstrapping methods are: QR code (good for devices that do not have a UI),
PKEX (use a shared symmetric code/key to parlay into a trusted public key,
good for devices that have at least some limited UI), NFC.

 * What form of credential is installed into the device?
 
A signed EC public key appropriate for DPP authentication (a new AKM). Also,
for support of legacy (non-DPP) networks a PSK/password/passphrase can be
provisioned.

 * Is online access required for onboarding?
 
No.

 * Who becomes the root of trust at the end of onboarding (if any)

The Configurator.

 * Could/Is the resulting credential be used for application identity?

Not directly, and not without some work/hacking. It is a DPP-network specific credential.

 * What happens if the box gets reset?

The provisioned DPP data, including the credential, are wiped. The device goes back into
a pre-provisioned state and will take part in bootstrapping again.

THis is the "Resurrecting Duckling" trust model

 * How can transfer of ownership occur?
 
Let another Configurator proceed through bootstrapping-authentication-provisioning
with the device.

 * What sort of manufacturing requirements are there?
 
Device must be have a public key generated/installed at manufacturing time. Depending
on the device the public key may have to be encoded in a QR code which is either
slapped on the backside of the device or provided in packing materials.

 * What sort of crypto requirements are there?

ECC only.

 * Reference link

[Device Provisioning Protocol Specification](https://www.wi-fi.org/downloads-registered-guest/Device_Provisioning_Protocol_Specification_v1.0.pdf/35330)

## WPA2-PSK

## Hotspot 2.0 OSU

## EAP-NOOB

## CoAP-EAP

## BRSKI-CoAP-EST

## Template-Based Reprovisioning

### Template Re-Provisioning via EAP

## Basic CMP (RFC 4210)

## Basic BRSKI

 * How does device introduce itself to the infrastructure or peer?

A manufacturer certificate and trust anchor is installed in the device at build time.  During onboarding, that information is passed to a join registrar, which adds additional information and passes it to the manufacturer authorized signing authority (MASA), which then returns a voucher.

 * What, if any, proof of possession mechanism is there?

Basic BRSKI requires back-end sales integration to know if a device belongs on a particular network.

 *  What form of credential is installed into the device?

An X.509 certificate that service as a root of trust, via a voucher.

 * Is online access required for onboarding?

Yes.  The MASA is an online service.

 * Could/Is the resulting credential be used for application identity?

Yes.

 * What happens if the box gets reset?

The BRSKI process would have to be rerun.  The MASA service would be required again.

 * How can transfer of ownership occur?

 BRSKI would require that either the device consider the current LDEVID (deployment cert) as an IDEVID (manufacturer cert) or that the transfer of ownership be recorded by the MASA.

## Wi-SUN (version 1)

 * How does device introduce itself to the infrastructure or peer?
 
 EAP-TLS
 
 * What, if any, proof of possession mechanism is there?
 
 Infra is expected to have list of registered devices.
 
 * What form of credential is installed into the device?
 
 Devices run off of manufacturer certs.
 
 * Is online access required for onboarding?
 
 No
 
 * Who becomes the root of trust at the end of onboarding (if any)
 
 Trust is manually configured.
 
 * Could/Is the resulting credential be used for application identity?
 
 Yes (but it is the manufacturer certificate).
 
 * What happens if the box gets reset?
 
 You still use the manufacgturer certificate.
 
 * How can transfer of ownership occur?
 
Not specified in standard.  In v1, device accepts cert.
 
 * What sort of manufacturing requirements are there?
 
 Certificate/trust anchor installation.
 
 * What sort of crypto requirements are there?
 
 TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CCM\_8 as MTI.
 
 * Reference link

Spec not publsihed but see https://tools.ietf.org/id/draft-heile-lpwan-wisun-overview-00.html.
## Zigbee

## Bluetooth

## AllJoyn Onboarding

 * How does device introduce itself to the infrastructure or peer?

The device starts as a SoftAP, and so advertises WiFi beacons, using an SSID that either starts with AJ\_
or ends with \_AJ.

 * What, if any, proof of possession mechanism is there?

A QR code can be used to provide proof of possession of the device.

 * Is access to other IP-based devices required in order to fully onboard the device?

No.

 * What form of credential is installed into the device?

Such information minimally includes L2 credentials
(WiFi SSID and key), and typically ownership transfer is done at the same time, before a
device is put on the regular WiFi network, such that the device is provisioned with the
owner's cert and a certificate of owner-allowed device capabilities.

 * Is full Internet access required for onboarding?

No.

 * Who becomes the root of trust at the end of onboarding (if any)

The new owner's certificate becomes the root of trust.

 * Could/Is the resulting credential be used for application identity?

The question is bad because "the" assumes that onboarding only provisions one thing.
Application identity can be provisioned along with L2 credentials, which may be different.

 * What happens if the box gets reset?

A factory reset will typically remove all credentials, leaving the device ready to be
onboarded again.

 * How can transfer of ownership occur?

Ownership transfer is described in 
[AllJoyn Security 2.0](https://web.archive.org/web/20170730231028/http://allseenalliance.org/framework/documentation/learn/core/security2_0/hld).
In general, before the device is put onto the network, the new owner's certificate
is provisioned into the device so that by the time the device gets access to the
regular network, it is already fully configured with all security credentials.

 * How can transfer to a different cloud service (if applicable) occur?

Not applicable (not tied to a cloud service).

 * What sort of manufacturing requirements are there?

The QR code must be printed somewhere.

 * What sort of crypto requirements are there?

Uses X.509 certificates.
Uses ECDHE\_ECDSA, or ECDHE\_PSK, or ECDHE\_NULL for key exchange.

 * Reference link

[AllJoyn Onboarding Framework Interface Definition](https://web.archive.org/web/20170924015202/https://allseenalliance.org/framework/documentation/learn/base-services/onboarding/interface)

[AllJoyn Security 2.0](https://web.archive.org/web/20170730231028/http://allseenalliance.org/framework/documentation/learn/core/security2_0/hld)

project_path: /web/_project.yaml
book_path: /web/updates/_book.yaml
description: Generic Sensor API available for Origin Trials in Chome 62.

{# wf_updated_on: 2017-09-02 #}
{# wf_published_on: 2017-09-02 #}
{# wf_tags: sensors,origintrials,chrome62,news #}
{# wf_blink_components: Blink>Sensor #}
{# wf_featured_image: /web/updates/images/generic/sd-card.png #}
{# wf_featured_snippet: Generic Sensor API available for Origin Trials in Chome 62. #}

# Sensors For The Web! {: .page-title }

{% include "web/_shared/contributors/alexshalamov.html" %}
{% include "web/_shared/contributors/pozdnyakov.html" %}

Today, sensor data is used in many native applications to enable use cases such
as immersive gaming, fitness tracking, augmented or virtual reality. Wouldn't
it be cool to bridge the gap between native and web applications? The [Generic Sensor
API](https://www.w3.org/TR/generic-sensor/), FTW!

## What is Generic Sensor API? {: #what-is-generic-sensor-api }

The [Generic Sensor API](https://www.w3.org/TR/generic-sensor/) is a framework
that provides consistent interfaces for sensor devices exposed to the web
platform, simplifies implementation and specification process for the new
sensors. It consists of the base [Sensor](https://w3c.github.io/sensors/#the-sensor-interface)
interface and a set of concrete sensor classes. Look for example at the
[Gyroscope](https://w3c.github.io/gyroscope/#gyroscope-interface) interface,
it is really tiny! The core functionality is specified by the base interface,
and Gyroscope merely extends it with reading attributes.

Typically a concrete sensor class represents an actual sensor on the platform
e.g., accelerometer. However, in some cases, sensors [fuse](https://w3c.github.io/sensors/#sensor-fusion)
data from several sources and expose processed data in a convenient way to the
user. For example, [AbsoluteOrientation](https://www.w3.org/TR/orientation-sensor/#absoluteorientationsensor)
sensor provides ready-to-use 4x4 rotation matrix based on the data obtained
from accelerometer, gyroscope and magnetometer.

You might think that the Web platform already provides sensor data and you are
absolutely right! For instance, [DeviceMotion](https://www.w3.org/TR/2016/CR-orientation-event-20160818/#devicemotion)
and [DeviceOrientatrion](https://www.w3.org/TR/2016/CR-orientation-event-20160818/#deviceorientation)
events expose motion sensor data, while other experimental APIs provide data
from an environmental sensors. So why do we need new API?

Comparing to the existing interfaces, Generic Sensor API provides great
number of advantages:

- You can configure sensors. For example, request data delivery rate suitable
  for your application.
- You can detect whether sensor is available on the platform.
- Sensor readings have high precision timestamps, enabling synchronization
  with other activities.
- Sensor data models and coordinate systems are clearly defined, allowing
  browser vendors to implement interoperable solutions.
- The Generic Sensor based interfaces are not bound to DOM (neither navigator
  nor window objects), and it opens up new opportunities of using same API from
  service workers or implementing Generic Sensor API in headless JS runtimes.
- Security and privacy aspects are the top priority for the Generic Sensor API
  and provide much better security level compared to older sensor APIs. There
  is integration with Permissions API.

## Generic Sensor APIs in Chrome {: #generic-sensor-api-in-chrome }

At the time of writing, Chrome supports several sensors that you can experiment with.

**Motion sensors:**

- Accelerometer
- Gyroscope
- LinearAccelerationSensor
- AbsoluteOrientationSensor
- RelativeOrientationSensor

**Environmental sensors:**

- AmbientLightSensor
- Magnetometer

You can enable Generic Sensor APIs for development purposes by turning a
feature flag. Go to [chrome://flags/#enable-generic-sensor](chrome://flags/#enable-generic-sensor)
to enable motion sensors or
[chrome://flags/#enable-generic-sensor-extra-classes](chrome://flags/#enable-generic-sensor-extra-classes)
to enable environmental sensors. Restart Chrome and you should be good to go.

<iframe width="100%" height="320"
  src="https://www.chromestatus.com/feature/5698781827825664?embed"
  style="border: 1px solid #CCC" allowfullscreen>
</iframe>

More information on browser implementation status can be found on
[chromestatus.com](https://www.chromestatus.com/features/5698781827825664?embed)

## Motion sensors available for Origin Trials {: #motion-sensors-origin-trials }

In order to get your valuable feedback, the Generic Sensor API would be
available in Chrome 62 as an [origin trial](https://bit.ly/OriginTrials). You
will need to [request a token](http://bit.ly/OriginTrialSignup), so that the
feature would be automatically enabled for your origin, without the need to
enable chrome flag.

## What are all these sensors? How can I use them? {: #what-are-sensors-how-to-use-them }

Sensors is a quite specific area which might need a brief introduction to the
people who never used them before. If you are familiar with sensors, you can
jump right to the [hands-on coding section](#lets-code). Otherwise, let’s look at each
supported sensor in more detail.

### Accelerometer and Linear acceleration sensor {: #acceleration-and-linear-accelerometer-sensor }

<div class="attempt-right">
  <figure>
    <img src="/web/updates/images/2017/09/sensors/accelerometer.gif" alt="Accelerometer sensor measurements">
    <figcaption><b>Figure 1</b>: Accelerometer sensor measurements</figcaption>
  </figure>
</div>

The [Accelerometer](https://www.w3.org/TR/accelerometer/#intro) sensor measures
acceleration of a device hosting the sensor on three axes (X, Y and Z). This
sensor is an inertial sensor, meaning that when the device is in linear free
fall, the total measured acceleration would be 0 m/s<sup>2</sup>, and when a
device lies flat on a table, the acceleration in upwards direction (Z axis)
will be equal to the Earth’s gravity, i.e. g ≈ +9.8 m/s<sup>2</sup> as it is
measuring the force of the table pushing the device upwards. If you push
device to the right, acceleration on X axis would be positive, or negative
if device is accelerated from right toward the left.

Accelerometers can be used for things like: step counting, motion sensing or
simple device orientation. Quite often, accelerometer measurements are combined
with data from other sources in order to create fusion sensors, such as,
orientation sensors.

The [LinearAccelerationSensor](https://w3c.github.io/accelerometer/#linearaccelerationsensor-interface)
measures acceleration that is applied to the device hosting the sensor,
excluding the contribution of a gravity force. When a device is at rest, for
instance, lies flat on the table, sensor would measure ≈ 0 m/s<sup>2</sup>
acceleration on three axes.

### Gyroscope {: #gyroscope-sensor }

<div class="attempt-right">
  <figure>
    <!--Animation, device is rotated around axes, acceleration changes according to rotation.-->
    <img src="https://placehold.it/350x200" alt="Animation of how gyroscope works">
    <figcaption><b>Figure 2</b>: Gyroscope sensor</figcaption>
  </figure>
</div>

The [Gyroscope](https://w3c.github.io/gyroscope/#intro) sensor measures angular
velocity in rad/s around the device’s local X, Y and Z axis. Most of the
consumer devices have mechanical
([MEMS](https://en.wikipedia.org/wiki/Microelectromechanical_systems))
gyroscopes, which are inertial sensors that measure rotation rate based on
[inertial Coriolis force](https://en.wikipedia.org/wiki/Coriolis_force). A MEMS
gyroscopes are prone to drift that is caused by sensor’s gravitational
sensitivity which deforms sensor’s internal mechanical system. Gyroscopes
oscillate at relative high frequencies e.g., 10’s of kHz, therefore, might
consume more power compared to other sensors.

### Orientation sensors {: orientation-sensors }

<div class="attempt-right">
  <figure>
    <!--Animation, device is rotated in relation to Earth, show how values are changed.
    Quat or rot.matrix?-->
    <img src="https://placehold.it/350x200" alt="Animation of how orientation sensor works">
    <figcaption><b>Figure 3</b>: Orientation sensor</figcaption>
  </figure>
</div>

The [AbsoluteOrientationSensor](https://w3c.github.io/orientation-sensor/#orientationsensor-interface)
is a fusion sensor that measures rotation of a device in relation to the
Earth’s coordinate system, while the
[RelativeOrientationSensor](https://w3c.github.io/orientation-sensor/#relativeorientationsensor-interface)
provides datarepresenting rotation of a device hosting motion sensors in
relation to a stationary reference coordinate system.

Orientation sensors enable various use cases, such as, immersive gaming,
augmented and virtual reality.

For more information about motion sensors, advanced use cases and requirements,
please check [motion sensors explainer](https://www.w3.org/TR/motion-sensors/)
document.

## Let’s code! {: #lets-code }

The Generic Sensor API is very simple and easy-to-use! The Sensor interface has
`start()` and `stop()` methods to control sensor state and several event handlers for
receiving notifications about sensor activation, errors and newly available
readings. The concrete sensor classes usually add their specific reading
attributes to the base class.

In this simple example, we use data from an absolute orientation sensor to
modify rotation quaternion of a 3D model.

```
function initSensor() {
    if ('AbsoluteOrientationSensor' in window) {
        sensor = new AbsoluteOrientationSensor({frequency: 60});
        sensor.onreading = () => model.quaternion.fromArray(sensor.quaternion);
        sensor.start();
    }
}
```

Whenever the device is rotated, model’s rotation quaternion would be updated
thus, rotating the model in WebGL scene.

<figure>
  <img class="screenshot" src="https://placehold.it/350x200" alt="Image for the orientation demo">
  <figcaption>Image or gif for the demo.</figcaption>
</figure>


## Privacy and Security {: privacy-and-security }

Sensor readings are sensitive data which can be subject to various attacks from
malicious web pages. Chrome implementation of Generic Sensor APIs embarked on
few limitations to mitigate the possible security and privacy risks. These
limitations must be taken into account by developers who intend to use the API,
so let’s briefly enumerate them.

### Only HTTPS

Because Generic Sensor API is a powerful new feature added to the Web, Chrome
aims to make it available only to secure contexts. In practice it means that
in order to use Generic Sensor API you'll need to access your page through
HTTPS. During the development you can do so via http://localhost but for
production deployment you'll need to have HTTPS set up on your server. See
Security with HTTPS article for best practices and guidelines there.

### Only main frame

To prevent iframes from reading sensor data the Sensor objects can be created
only within main frame context.

### Sensor readings delivery can be suspended

Sensor readings are only accessible by a visible web page, i.e., when the user
is actually interacting with it. Moreover, sensor data would not be provided
if the user focuses from a main frame to a cross-origin iframe, so that the
main frame cannot infer user input.

### Limited sample rate
Sensor readings are delivered at maximum 60 times per second.


## What’s next? {: whats-next }

The specification is currently a draft which means that it there is still
time to make fixes that developers need.

- Multiphase plan for Origin Trials
- Proximity sensor
- Gravity sensor
- Geomagnetic Orientation sensor

## You can help!

List features that need attention, feedback.

Make Google form survey with problem desciption, ask to select possible
solutions + free field for proposal.

- Expose sensors to service workers
- Provide sensor readings at higher rates (>60Hz)
- Allow changing sensor options after construction (e.g., frequency)
- Provide solution for synchronization of sensor and screen coordinate systems
- Improve sensor features detection
- Provide better mechanisms for the Sensor interface extensibility
- Clarify whether we need granular permission tokens for orientation sensor
  classes
- Provide new sensor types and/or options (e.g. accuracy)
- Feature policy to enable sensor access for iframes

## Resources

- Generic Sensor API specification: [https://w3c.github.io/sensors/](https://w3c.github.io/sensors/)
- Specification issues: [https://github.com/w3c/sensors/issues](https://github.com/w3c/sensors/issues)
- W3C working group mailing list: [public-device-apis@w3.org](public-device-apis@w3.org )
- Chrome Feature Status: [https://www.chromestatus.com/feature/5698781827825664](https://www.chromestatus.com/feature/5698781827825664 )
- Implementation Bugs: [http://crbug.com?q=component:Blink>Sensor](http://crbug.com?q=component:Blink>Sensor)
- Sensor-dev Google group: [https://groups.google.com/a/chromium.org/forum/#!forum/sensors-dev](https://groups.google.com/a/chromium.org/forum/#!forum/sensors-dev)

{% include "comment-widget.html" %}

---
title: "Example: Plain Old Static HTML"
layout: docs
---

The best indicator of a healthy development workflow is a short feedback loop.

Kubernetes is a huge wrench in the works.

Let's fix this.

In this example, we're going to take you through a very simple shell script that
serves static HTML.

We'll use Tilt to:

- Run the server on Kubernetes
- Measure how long it takes to deploy
- Optimize the updates for fast feedback.

Obviously, this is a silly example. But it can be a useful example to confirm that Tilt is working
as expected in your environment.

All the code is in this repo:

[tilt-example-html](https://github.com/windmilleng/tilt-example-html)

To skip straight to the fully optimized setup, go to this subdirectory:

[Recommended Tiltfile](https://github.com/windmilleng/tilt-example-html/blob/master/2-recommended/Tiltfile)

## Step 0: The Simplest Deployment

Our server is a two-line shell script:

```shell
echo "Serving files on port 8000"
busybox httpd -f -p 8000
```

To start this server on Kubernetes, we need 3 configs:

1) A [Dockerfile](https://github.com/windmilleng/tilt-example-html/blob/master/0-base/Dockerfile) that builds the image.

2) A [Kubernetes deployment](https://github.com/windmilleng/tilt-example-html/blob/master/0-base/kubernetes.yaml) that runs the image

3) And finally, a Tiltfile that ties them together.

```python
docker_build('example-html-image', '.')
k8s_yaml('kubernetes.yaml')
k8s_resource('example-html', port_forwards=8000)
```

The first line tells Tilt to build an image with the name `example-html-image`
in the directory `.` (the current directory).

The second line tells Tilt to load the Kubernetes deployment yaml. The image name in the `docker_build`
call must match the image name in the `example-html` Deployment.

The last line configures port-forwarding so that your server is
reachable at http://localhost:8000/. The resource name in the `k8s_resource` call
must match the deployment name in the Kubernetes yaml.

Try it! Run:

```
git clone https://github.com/windmilleng/tilt-example-html
cd tilt-example-html/0-base
tilt up
```

Your terminal will turn into a status box that lets you watch your server come
up. When it's ready, you will see the status icon turn green. The logs in the
botton pane will display "Serving files on port 8000." The snapshot below
lets you see what it should look like.

[Snapshot](https://cloud.tilt.dev/snapshot/AejkyuULr2AjWu50Eck=)

## Step 1: Let's Add Benchmark Trickery

Before we try to make this faster, let's measure it.

Tilt has two major escape hatches for running commands locally:

- `local(command)` which runs a local command for its output

- `local_resource(name, command)` which lets you run local jobs to trigger new updates, or run local servers

For more detail, see the [local resource guide](local_resource.html)

We add a `local_resource` to our
[Tiltfile](https://github.com/windmilleng/tilt-example-html/blob/master/1-measured/Tiltfile)
that records when an update starts.

```python
k8s_resource('example-html', port_forwards=8000, resource_deps=['deploy'])

# Records the start time.
# When the contents of this directory change, example-html will automatically re-deploy.
local_resource(
  'deploy',
  'date +%s > start-time.txt',
  trigger_mode=TRIGGER_MODE_MANUAL)
```

The `local_resource()` call creates a local resource named `deploy`. The second
argument is the script that it runs. The third argument adds a button to the
Tilt UI, to re-start the deploy.

Let's click the deploy button and see what happens!

[Snapshot](https://cloud.tilt.dev/snapshot/AcD7yuUL6_d3neimWHk=)

| Approach | Deploy Time |
|---|---|
| Naive | 1-2s |

Can we do better?

## Step 2: Let's Optimize It

When we make a change to a file, we currently have to build an image, deploy new Kubernetes configs,
and wait for Kubernetes to schedule the pod.

With Tilt, we can skip all of these steps, live-updating the pod in place.

Here's our [new Tiltfile]([Tiltfile](https://github.com/windmilleng/tilt-example-html/blob/master/2-recommended/Tiltfile) 
with the following new code:

```python
# Add a live_update rule to our docker_build.
docker_build('example-html-image', '.', live_update=[
  sync('.', '/app'),
  run('./report-deployment-time.sh'),
])
```

We've added a new parameter to `docker_build()` with two `live_update` steps.

The first step syncs the code from the current directory (`.`) into the container at directory `/app`.

The second step runs our script to report the deployment time. This is just for measuring!

Let's see what this looks like:

[Snapshot](https://cloud.tilt.dev/snapshot/AbCby-ULsvPkLPQWuQw=)

In this snapshot, we see the logs:

```
STEP 1/1 — updating image registry:5000/example-html-image
     Updating container: 10e2961315
Will copy 1 file(s) to container: 10e2961315
- '/home/nick/src/tilt-example-html/2-recommended/start-time.txt' --> '/app/start-time.txt'
[CMD 1/1] sh -c ./report-deployment-time.sh
Deploy time in seconds: 0
  → Container 10e2961315 updated!

     Step 1 - 0.17s
     DONE IN: 0.17s 
```

Tilt was able to update the container in less than a second!

## Our Recommendation

### Final Score

| Approach | Deploy Time |
|---|---|
| Naive | 1-2s |
| With live_update | 0-1s |

You can try the server here:

[Recommended Structure](https://github.com/windmilleng/tilt-example-html/blob/master/2-recommended)

Obviously, our busybox example is very silly. We just wanted to show you how
Tilt can work with any language, even a silly one.

Other examples:

<ul>
  {% for page in site.data.examples %}
     {% if page.href contains "static_html" %}
       <!-- skip -->
     {% else %}
        <li><a href="/{{page.href | escape}}">{{page.title | escape}}</a></li>
     {% endif %}
  {% endfor %}
</ul>

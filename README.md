# Docker VPS plans: how much RAM your containers really need, which AVA Hosting tier to pick from €2 to €40, and the storage limit most plan pages hide (full comparison)

Two numbers decide your Docker VPS purchase: the RAM your containers add up to, and whether the disk grows when you move up a tier. Get the first wrong and the kernel's OOM killer starts terminating containers. Get the second wrong and you're migrating hosts a month after deploying.

The sizing baseline hasn't moved much. A single small app plus a database fits in 2GB of RAM. A busy production stack with a few services wants 2 to 4 vCPUs and 4 to 8GB. Docker Engine itself idles at roughly 200MB of RAM and barely touches the CPU, so almost your entire budget goes to the containers on top.

AVA Hosting sells a six-tier self-hosted lineup from €2 to €40 per month, plus a parallel VPS/VDS lineup at the same price points with different storage and slightly different specs. Both are below, followed by which tier each common workload actually needs and the limits that only surface after deployment.

> **Quick pick:** one app plus a database on a personal server points at **Mini: 1 vCPU, 2GB RAM, 25GB NVMe, €5.00/month**. Running several Compose projects behind a reverse proxy pushes you to **Basic at €10.00/month for 2 vCPU, 4GB and 50GB**. Most readers never need anything above Pro. 👉 [See the current AVA Hosting self-hosted plans](https://my.ava.hosting/aff.php?aff=15562)

## AVA Hosting's two lineups, side by side

Prices are shown as "starting from" in euros, billed monthly. The self-hosted lineup carries a free trial badge on its page; the length and conditions aren't stated there, so confirm before counting on it.

| Plan | vCPU / RAM | NVMe storage | Price | Order |
| --- | --- | --- | --- | --- |
| Start | 1 vCPU / 1GB | 15GB | €2.00/mo | [Order Start](https://my.ava.hosting/aff.php?aff=15562) |
| Mini | 1 vCPU / 2GB | 25GB | €5.00/mo | [Order Mini](https://my.ava.hosting/aff.php?aff=15562) |
| Basic | 2 vCPU / 4GB | 50GB | €10.00/mo | [Order Basic](https://my.ava.hosting/aff.php?aff=15562) |
| Pro | 4 vCPU / 8GB | 100GB | €20.00/mo | [Order Pro](https://my.ava.hosting/aff.php?aff=15562) |
| Business | 6 vCPU / 12GB | 150GB | €30.00/mo | [Order Business](https://my.ava.hosting/aff.php?aff=15562) |
| Power | 8 vCPU / 16GB | 200GB | €40.00/mo | [Order Power](https://my.ava.hosting/aff.php?aff=15562) |

Every tier lists unlimited transfer.

The VPS/VDS lineup is a separate product page with its own names, storage figures and extras:

| Plan | vCPU / RAM | NVMe storage | Port speed | Price |
| --- | --- | --- | --- | --- |
| VPS Nano | 1 vCPU / 512MB | 20GB | 100Mbps | €11.00/year (0 available) |
| VPS One | 1 vCPU / 2GB | 25GB | 1Gbps | €5.00/mo |
| VPS Basic | 2 vCPU / 4GB | 50GB | 1Gbps | €10.00/mo |
| VPS Lucky | 2 vCPU / 6GB | 70GB | 1Gbps | €15.00/mo |
| VPS Power | 4 vCPU / 8GB | 90GB | 1Gbps | €20.00/mo |
| VPS Extra | 6 vCPU / 12GB | 120GB | 1Gbps | €30.00/mo |
| VPS Ultra | 8 vCPU / 16GB | 160GB | 1Gbps | €40.00/mo |

That page lists Intel Xeon E5 CPUs, ECC RAM, free DDoS protection, full root access, a control panel, weekly backups, 24/7 support and a 99.9% uptime SLA as standard. The €20 comparison is worth a second look: Pro on the self-hosted page gets you 100GB of NVMe, and VPS Power at the same €20 gets 90GB but states the backup and SLA terms explicitly. Same money, different trade.

## Why 2GB is not a small machine

Containers share the host kernel and package only the application and its dependencies. A virtual machine runs a full guest operating system, and that OS alone can consume 1 to 2GB of RAM before your software starts. That's why a 2GB VPS running Docker can hold several containers while a 2GB VM would struggle with one.

Docker Engine's own footprint is about 200MB when idle. On a 2GB box that leaves roughly 1.8GB for real work, which is enough for one application container plus PostgreSQL or MySQL, or for three or four light services. It is not enough for an image-heavy database, a Jellyfin transcode, and a build pipeline running at the same time.

The practical constraint is root access, not RAM. The Docker daemon needs root-level privileges to manage containers, networks and kernel features like namespaces and cgroups. Shared hosting doesn't grant that. Every AVA plan includes full root access, which is the actual prerequisite.

## Which tier fits your workload

**One container, no database.** A reverse proxy, a status page, a small API, a single n8n instance. Start at €2 with 1 vCPU, 1GB and 15GB. Engine takes 200MB, leaving about 800MB, and that's tight enough that you'll want to watch memory. If the container is anything beyond trivial, the €3 jump to Mini buys you headroom you'll use.

**Personal self-hosting with a database.** Nextcloud, Immich, n8n, Plex or Jellyfin each have an official or community-maintained image ready to pull, and the usual pattern is one app container plus a PostgreSQL container. Mini at €5 covers the compute. The 25GB disk is where this gets uncomfortable: photo libraries and media metadata outgrow it quickly. If you're storing photos or media, pay the extra €5 for Basic's 50GB rather than fighting the disk later.

**Several Compose projects on one box.** A YAML file describes the services, networks and volumes, `docker compose up` brings the stack online, and a reverse proxy such as Nginx Proxy Manager, Caddy or Traefik routes each domain to the right container and handles Let's Encrypt certificates. Two or three small stacks plus a proxy is Basic territory: 2 vCPU, 4GB, 50GB, €10.

**A production stack with traffic.** A few services, a real database, backups running on a schedule, and logs you actually read. This is where 4 vCPU and 8GB stops being overkill, so Pro at €20. A heavy-write database benefits more from the NVMe volume than from extra cores, which is why 100GB at this tier matters as much as the RAM.

Business and Power exist for build runners, multiple databases, or a dozen services on one host. If you're pricing those tiers for a personal server, you're buying capacity you won't use.

## Storage is the tier that bites first

Notice how AVA's storage moves: 15GB, 25GB, 50GB, 100GB, 150GB, 200GB. Every step up doubles or near-doubles the disk alongside the RAM, which is not universal in this market. LightNode's three published tiers all sit at 50GB of NVMe whether you pay $7.71 or $52.70, and MonoVM scales from 30GB to 150GB across five tiers. If you plan to run Immich, Nextcloud or anything that accumulates files, the storage column deserves more of your attention than the vCPU count. A slow or full disk throttles Docker volumes long before the CPU becomes the bottleneck.

## Limits and costs that don't appear in the plan table

**Containers are ephemeral.** Anything written inside a container's writable layer disappears when the container is replaced or upgraded. Named volumes store data on the host filesystem, and those volumes are what you back up. Backing up a container image is not a backup.

**Check what's included per lineup.** Weekly backups are listed on the VPS/VDS page. The self-hosted page doesn't mention backups in its feature list, so if backup matters to you, ask which line you're buying and what runs automatically.

**Port 25.** The VPS Nano spec sheet marks port 25 as closed. If you intend to send mail from a container, confirm the policy on whichever plan you pick before you migrate a mail server.

**Docker Desktop licensing is a non-issue here.** Engine, the CLI and the container format are open source and free. Only Docker Desktop for Mac and Windows carries a paid commercial licence, and that licence is free for businesses under 250 employees and $10 million in annual revenue. On a Linux VPS you install Engine and work from a shell, so the Desktop terms rarely apply.

**No Docker template is listed on the plan pages.** Root access is included on every tier, so installing Engine from Docker's official apt repository over SSH is a ten-minute job, but plan for that step rather than expecting a pre-built image. 👉 [Check the VPS/VDS specs and what each plan includes](https://my.ava.hosting/aff.php?aff=15562)

## How the €2 to €40 range compares elsewhere

LightNode publishes $7.71/month for 1 vCPU, 2GB, 50GB NVMe and 1TB of transfer, $27.70 for 4 vCPU and 8GB, and $52.70 for 8 vCPU and 16GB, all with 50GB of storage. Hourly billing at $0.012 works out to about $8.76 across a full 730-hour month, so the monthly rate wins for anything always-on.

MonoVM lists five App VPS tiers from 1 vCPU/1GB/30GB to 4 vCPU/8GB/150GB, with no monthly figures shown on the page, only term discounts. It does publish Geekbench 6 multi-core results from its own Amsterdam hardware: 2,724 at 2GB, 4,952 at 4GB, 5,973 at 8GB and 8,288 at 16GB. Those are vendor-run tests on an idle single-tenant host, useful as a relative curve rather than an absolute promise.

Hostinger is running a Black Friday promotion showing $19.49 down to $6.49/month, with 8 vCPU, 32GB, 400GB NVMe and 32TB of bandwidth displayed on the same screen. The page doesn't state which plan the $6.49 applies to, so treat that figure as a promotional entry price rather than an 8-vCPU price. It renews higher.

Against that backdrop, €5 for 2GB with 25GB of NVMe and €20 for 8GB with 100GB sit at the low end of the range, with the caveat that prices are in euros and your card's conversion applies.

## Picking in one line

One lightweight container and nothing else: Start at €2. One app plus a database on a personal server: Mini at €5, or Basic at €10 if you're storing photos or media. Several Compose stacks behind Traefik: Basic at €10. A production stack with real traffic and a database: Pro at €20. Build runners or a dozen services: Business or Power, and only then.

Before you pay, confirm four things: whether Docker comes pre-installed or you install Engine yourself, whether you can add storage later without changing plans, the billing term and what the renewal price will be (the pages show "starting from" monthly figures), and whether the weekly backup applies to the lineup you chose. The free trial badge on the self-hosted page is worth asking about too, since its conditions aren't published.

👉 [Start with the €5 Mini plan or compare all six AVA Hosting tiers](https://my.ava.hosting/aff.php?aff=15562)

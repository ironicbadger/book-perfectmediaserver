# Welcome

If you're looking to build a media server, then you've come to the right place. This site documents the many aspects of building a media server using Free and Open Source Software wherever possible.

The primary technologies we recommend are [Linux](https://www.linux.org/), Containers (via [docker](https://www.docker.com/) and managed using [docker-compose](https://docs.docker.com/compose/)), [Proxmox](https://www.proxmox.com/en/), [MergerFS](https://github.com/trapexit/mergerfs/), [SnapRAID](http://www.snapraid.it/) and [ZFS on Linux](https://zfsonlinux.org/).

<div class="row">
  <div class="col-sm-8">
<p>Use the navigation on the left or the search in the top right to find your way around. If you found this site useful, please consider a small token of appreciation via 'buy me a coffee'.</p>
  </div>
  <div class="col-sm">
    <br />
    <style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#FFFFFF !important;background-color:#FF813F !important;border-radius: 8px !important;border: 1px solid transparent !important;font-size: 24px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#FFFFFF !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/alexktz"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:24px !important;">Buy me a coffee</span></a>
  </div>
</div>

## The aim of this site

To share knowledge and information regarding the software side of building a media server. There are plenty of other great resources for hardware such as [serverbuilds.net](https://serverbuilds.net). There is a short hardware section which covers some basics for those who are totally new to all this.

The potential scope of this project could be vast. There are many flavours of Linux, many hypervisors and so on. Therefore, in order that this documentation remains manageable, we only cover a small subset of the options available. However, by sharing the knowledge and thought patterns used to drive our decisions, you will leave armed with the ability to build your own DIY NAS or as we like to call it, the Perfect Media Server.

## About the series

Perfect Media Server began life as a series of blog posts over at [blog.linuxserver.io](https://blog.linuxserver.io/tag/perfectmediaserver/). Those posts continue to be very popular, but with so much information to be communicated something with search and a table of contents was needed.

You can find the previous incarnations of PMS below:

- [Perfect Media Server (2016 Edition) - The Original Article](https://blog.linuxserver.io/2016/02/02/the-perfect-media-server-2016/)
- [Perfect Media Server (2017 Edition) - Extensive video guides](https://blog.linuxserver.io/2017/06/24/the-perfect-media-server-2017/)
- [Perfect Media Server (2019 Edition) - Boring is reliable + adding ZFS](https://blog.linuxserver.io/2019/07/16/perfect-media-server-2019/)

I am no longer involved with LSIO, so it seemed like a good idea to put all of this information in one place and provid it in an open-source format. This site is created using [mdbook](https://github.com/rust-lang/mdBook) and PRs will be gladly considered via Github (using the icon in the top right) if you'd like to correct something here or share your own pearls of wisdom.

## About the Author

<div class="row">
  <div class="col-sm-4">
    <div class="circletag" id="nay">
        <img src="../images/alex.jpg">
    </div>
  </div>
  <div class="col-sm-8">

The Perfect Media Server is written and maintained by [Alex Kretzschmar](https://www.linkedin.com/in/alex-kretzschmar/) (aka [@IronicBadger](https://twitter.com/ironicbadger)). Alex is a Red Hatter by day and a giant nerd by night.

If you like this kind of thing, check out the [Self-Hosted](https://selfhosted.show) podcast over at [Jupiter Broadcasting](https://jupiterbroadcasting.com).

I also write technical posts over on my blog at [blog.ktz.me](https://blog.ktz.me) covering topics from Openshift to Terraform to electronics to Home Automation.
  </div>
</div>
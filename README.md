# Final-Project-Assessment-for-Scalefocus-Academy

Deploy a WordPress on Kubernetes (using Minicube) with Helm and
automation with Jenkins.

Prerequisites:
1. Install the necessary tools: Minicube, Helm and Jenkins.
2. Separate repo in your GitHub Profile named: Final Project Assessment for Scalefocus Academy
Requirement for the Project Assessment:
1. Download Helm chart for WordPress. ( Bitnami chart:
https://github.com/bitnami/charts/tree/main/bitnami/wordpress )
2. In values.yaml, you need to change line 543 from type: LoadBalancer to type: ClusterIP ( Hint: there
will be one more problem when deploying. Resolve it. )
3. Create a Jenkins pipeline that checks if wp namespace exists, if it doesn’t then it creates one.
Checks if WordPress exists, if it doesn’t then it installs the chart.
4. Name the Helm Deployment as: final-project-wp-scalefocus.
5. Deploy the helm chart using the Jenkins pipeline.
6. Load the home page of the WordPress to see the final result.
7. Explain the project directly in a README.md file in your project repo.

We need to install Helm Carts, Minkube and Jenkins.

For setting up a Jenkins Cluster on Kubernetes, we will do the following:
we need to create four files **serviceAccount.yaml**,  **volume.yaml**, **deployment.yaml**,  **service.yaml'**. and execute them so jenkkins ke be installed on minikube cluster. Offical documentation [Jenkins](https://www.jenkins.io/doc/book/installing/kubernetes/#create-a-service-account)

We can pull the offical wordpress image from github bitnami/wordpress/ reposity, and edit values.yaml on line 543 replace LoadBalancer to type: ClusterIP

We have to edit the file values.yaml, on line 543 the **LoadBalancer to type: ClusterIP**

After we setup jenkins on minikube we can log in to jenikns and install required plugin **kuberentes** so we can create connection to our local cluster. Next we need to create new pipeline and connect to our github respository. Create Jenkinsfile we put our script for deplyoment, the file has 4 stages:

- Validate - stages check is the namespace **wp** exist, if not exist the namespace will be created
- Install - wordpress and helm chard, here we install helm binary, and install wordpress helm chart
- Make wordpress availble - in the stage we are running port forwarding, so the site can be acceess localy.
- Browse WordPress site stage - !!! The site can`t be accees from outside becouse we put ClusterIP in values.yaml, **Cluster ip** default type for service in kubernetis, this type of address can be used only inside the cluster.

![Deplyoment](./assets/images/11.png)
![Deplyoment](./assets/images/22.png)


        Started by user Darko Avramovski
        Obtained Jenkinsfile from git https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        [Pipeline] Start of Pipeline
        [Pipeline] node
        Running on Jenkins in /var/jenkins_home/workspace/wp_deplyoment
        [Pipeline] {
        [Pipeline] stage
        [Pipeline] { (Declarative: Checkout SCM)
        [Pipeline] checkout
        Selected Git installation does not exist. Using Default
        The recommended git tool is: NONE
        using credential 407050f0-92b3-483f-9ecd-188b31bef379
        Cloning the remote Git repository
        Cloning repository https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        > git init /var/jenkins_home/workspace/wp_deplyoment # timeout=10
        Fetching upstream changes from https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        > git --version # timeout=10
        > git --version # 'git version 2.30.2'
        using GIT_SSH to set credentials 
        [INFO] SELinux is present on the host and we could not confirm that it does not apply actively: will try to relabel temporary files now; this may complain if context labeling not applicable after all
        > /usr/bin/chcon --type=ssh_home_t /var/jenkins_home/workspace/wp_deplyoment@tmp/jenkins-gitclient-ssh12013771761797829803.key
        Verifying host key using known hosts file
        You're using 'Known hosts file' strategy to verify ssh host keys, but your known_hosts file does not exist, please go to 'Manage Jenkins' -> 'Configure Global Security' -> 'Git Host Key Verification Configuration' and configure host key verification.
        > git fetch --tags --force --progress -- https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git +refs/heads/*:refs/remotes/origin/* # timeout=10
        > git config remote.origin.url https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git # timeout=10
        > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
        Avoid second fetch
        > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
        Checking out Revision 98ff55b7b7e53bd95ef0d87af4acde22a18f29e7 (refs/remotes/origin/main)
        > git config core.sparsecheckout # timeout=10
        > git checkout -f 98ff55b7b7e53bd95ef0d87af4acde22a18f29e7 # timeout=10
        Commit message: "Final-Project-Assessment-for-Scalefocus-Academy"
        First time build. Skipping changelog.
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] withEnv
        [Pipeline] {
        [Pipeline] stage
        [Pipeline] { (Validate)
        [Pipeline] sh
        + echo Download required binaries
        Download required binaries
        + curl -L -s https://dl.k8s.io/release/stable.txt
        + curl -LOs https://dl.k8s.io/release/v1.27.1/bin/linux/amd64/kubectl
        + chmod +x kubectl
        + curl -Os https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64
        + mv jq-linux64 jq
        + chmod +x jq
        [Pipeline] script
        [Pipeline] {
        [Pipeline] sh
        + ./kubectl get ns wp -o jsonpath={.status.phase}
        [Pipeline] echo
        Active
        [Pipeline] echo
        wp namespace already created
        [Pipeline] }
        [Pipeline] // script
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] stage
        [Pipeline] { (Install WordPress helm Chart)
        [Pipeline] sh
        + curl -Os https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz
        + tar zxvf helm-v3.12.0-linux-amd64.tar.gz
        linux-amd64/
        linux-amd64/helm
        linux-amd64/README.md
        linux-amd64/LICENSE
        [Pipeline] sh
        + ./linux-amd64/helm dependency build ./helm/wordpress/
        Saving 3 charts
        Downloading memcached from repo oci://registry-1.docker.io/bitnamicharts
        Pulled: registry-1.docker.io/bitnamicharts/memcached:6.4.2
        Digest: sha256:ac800af4f9b6be921043eb5cd2ba07828ad6fc404b5762f2630657d9fdf5a6fe
        Downloading mariadb from repo oci://registry-1.docker.io/bitnamicharts
        Pulled: registry-1.docker.io/bitnamicharts/mariadb:12.2.2
        Digest: sha256:f18fd0e930041ef6a1dff0789eb801f2c4c52f1e8e0ff7c610b109ae8304d74c
        Downloading common from repo oci://registry-1.docker.io/bitnamicharts
        Pulled: registry-1.docker.io/bitnamicharts/common:2.2.5
        Digest: sha256:a088a039a53958fdd4ddff5a9799c0dba38d1c480bc768a9141cb87e7fcf7036
        Deleting outdated charts
        + ./linux-amd64/helm upgrade -i -f ./helm/wordpress/values.yaml final-project-wp-scalefocus ./helm/wordpress -n wp
        Release "final-project-wp-scalefocus" has been upgraded. Happy Helming!
        NAME: final-project-wp-scalefocus
        LAST DEPLOYED: Tue May 16 10:33:33 2023
        NAMESPACE: wp
        STATUS: deployed
        REVISION: 3
        TEST SUITE: None
        NOTES:
        CHART NAME: wordpress
        CHART VERSION: 16.1.2
        APP VERSION: 6.2.0

        ** Please be patient while the chart is being deployed **

        Your WordPress site can be accessed through the following DNS name from within your cluster:

        final-project-wp-scalefocus-wordpress.wp.svc.cluster.local (port 80)

        To access your WordPress site from outside the cluster follow the steps below:

        1. Get the WordPress URL by running these commands:

        kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 80:80 &
        echo "WordPress URL: http://127.0.0.1//"
        echo "WordPress Admin URL: http://127.0.0.1//admin"

        2. Open a browser and access WordPress using the obtained URL.

        3. Login with the following credentials below to see your blog:

        echo Username: user
        echo Password: $(kubectl get secret --namespace wp final-project-wp-scalefocus-wordpress -o jsonpath="{.data.wordpress-password}" | base64 -d)
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] stage
        [Pipeline] { (Make WordPress available)
        [Pipeline] sh
        + echo WordPress URL: http://127.0.0.1//
        WordPress URL: http://127.0.0.1//
        + echo WordPress Admin URL: http://127.0.0.1//admin
        WordPress Admin URL: http://127.0.0.1//admin
        + ./kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 8000:80
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] stage
        [Pipeline] { (Browse WordPress site)
        [Pipeline] sh
        + curl -v http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local
        % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                        Dload  Upload   Total   Spent    Left  Speed

        0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 10.98.185.81:80...
        * Connected to final-project-wp-scalefocus-wordpress.wp.svc.cluster.local (10.98.185.81) port 80 (#0)
        > GET / HTTP/1.1
        > Host: final-project-wp-scalefocus-wordpress.wp.svc.cluster.local
        > User-Agent: curl/7.74.0
        > Accept: */*
        > 
        * Mark bundle as not supporting multiuse
        < HTTP/1.1 200 OK
        < Date: Tue, 16 May 2023 10:33:36 GMT
        < Server: Apache
        < Link: <http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-json/>; rel="https://api.w.org/"
        < Vary: Accept-Encoding
        < Transfer-Encoding: chunked
        < Content-Type: text/html; charset=UTF-8
        < 
        { [8533 bytes data]
        <!DOCTYPE html>
        <html lang="en-US">
        <head>
                <meta charset="UTF-8" />
                <meta name="viewport" content="width=device-width, initial-scale=1" />
        <meta name='robots' content='max-image-preview:large' />
        <title>User&#039;s Blog!</title>
        <link rel="alternate" type="application/rss+xml" title="User&#039;s Blog! &raquo; Feed" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/feed/" />
        <link rel="alternate" type="application/rss+xml" title="User&#039;s Blog! &raquo; Comments Feed" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/comments/feed/" />
        <script>
        window._wpemojiSettings = {"baseUrl":"https:\/\/s.w.org\/images\/core\/emoji\/14.0.0\/72x72\/","ext":".png","svgUrl":"https:\/\/s.w.org\/images\/core\/emoji\/14.0.0\/svg\/","svgExt":".svg","source":{"concatemoji":"http:\/\/final-project-wp-scalefocus-wordpress.wp.svc.cluster.local\/wp-includes\/js\/wp-emoji-release.min.js?ver=6.2"}};
        /*! This file is auto-generated */
        !function(e,a,t){var n,r,o,i=a.createElement("canvas"),p=i.getContext&&i.getContext("2d");function s(e,t){p.clearRect(0,0,i.width,i.height),p.fillText(e,0,0);e=i.toDataURL();return p.clearRect(0,0,i.width,i.height),p.fillText(t,0,0),e===i.toDataURL()}function c(e){var t=a.createElement("script");t.src=e,t.defer=t.type="text/javascript",a.getElementsByTagName("head")[0].appendChild(t)}for(o=Array("flag","emoji"),t.supports={everything:!0,everythingExceptFlag:!0},r=0;r<o.length;r++)t.supports[o[r]]=function(e){if(p&&p.fillText)switch(p.textBaseline="top",p.font="600 32px Arial",e){case"flag":return s("\ud83c\udff3\ufe0f\u200d\u26a7\ufe0f","\ud83c\udff3\ufe0f\u200b\u26a7\ufe0f")?!1:!s("\ud83c\uddfa\ud83c\uddf3","\ud83c\uddfa\u200b\ud83c\uddf3")&&!s("\ud83c\udff4\udb40\udc67\udb40\udc62\udb40\udc65\udb40\udc6e\udb40\udc67\udb40\udc7f","\ud83c\udff4\u200b\udb40\udc67\u200b\udb40\udc62\u200b\udb40\udc65\u200b\udb40\udc6e\u200b\udb40\udc67\u200b\udb40\udc7f");case"emoji":return!s("\ud83e\udef1\ud83c\udffb\u200d\ud83e\udef2\ud83c\udfff","\ud83e\udef1\ud83c\udffb\u200b\ud83e\udef2\ud83c\udfff")}return!1}(o[r]),t.supports.everything=t.supports.everything&&t.supports[o[r]],"flag"!==o[r]&&(t.supports.everythingExceptFlag=t.supports.everythingExceptFlag&&t.supports[o[r]]);t.supports.everythingExceptFlag=t.supports.everythingExceptFlag&&!t.supports.flag,t.DOMReady=!1,t.readyCallback=function(){t.DOMReady=!0},t.supports.everything||(n=function(){t.readyCallback()},a.addEventListener?(a.addEventListener("DOMContentLoaded",n,!1),e.addEventListener("load",n,!1)):(e.attachEvent("onload",n),a.attachEvent("onreadystatechange",function(){"complete"===a.readyState&&t.readyCallback()})),(e=t.source||{}).concatemoji?c(e.concatemoji):e.wpemoji&&e.twemoji&&(c(e.twemoji),c(e.wpemoji)))}(window,document,window._wpemojiSettings);
        </script>
        <style>
        img.wp-smiley,
        img.emoji {
                display: inline !important;
                border: none !important;
                box-shadow: none !important;
                height: 1em !important;
                width: 1em !important;
                margin: 0 0.07em !important;
                vertical-align: -0.1em !important;
                background: none !important;
                padding: 0 !important;
        }
        </style>
                <style id='wp-block-site-title-inline-css'>
        .wp-block-site-title a{color:inherit}
        .wp-block-site-title{font-size: var(--wp--preset--font-size--medium);font-weight: normal;line-height: 1.4;}
        .wp-block-site-title a:where(:not(.wp-element-button)){text-decoration: none;}
        .wp-block-site-title a:where(:not(.wp-element-button)):hover{text-decoration: underline;}
        .wp-block-site-title a:where(:not(.wp-element-button)):focus{text-decoration: underline dashed;}
        .wp-block-site-title a:where(:not(.wp-element-button)):active{color: var(--wp--preset--color--secondary);text-decoration: none;}
        </style>
        <style id='wp-block-page-list-inline-css'>
        .wp-block-navigation .wp-block-page-list{align-items:var(--navigation-layout-align,initial);background-color:inherit;display:flex;flex-direction:var(--navigation-layout-direction,initial);flex-wrap:var(--navigation-layout-wrap,wrap);justify-content:var(--navigation-layout-justify,initial)}.wp-block-navigation .wp-block-navigation-item{background-color:inherit}
        </style>
        <link rel='stylesheet' id='wp-block-navigation-css' href='http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-includes/blocks/navigation/style.min.css?ver=6.2' media='all' />
        <style id='wp-block-navigation-inline-css'>
        .wp-block-navigation{font-size: var(--wp--preset--font-size--small);}
        .wp-block-navigation a:where(:not(.wp-element-button)){color: inherit;text-decoration: none;}
        .wp-block-navigation a:where(:not(.wp-element-button)):hover{text-decoration: underline;}
        .wp-block-navigation a:where(:not(.wp-element-button)):focus{text-decoration: underline dashed;}
        .wp-block-navigation a:where(:not(.wp-element-button)):active{text-decoration: none;}
        </style>
        <style id='wp-block-group-inline-css'>
        .wp-block-group{box-sizing:border-box}
        </style>
        <style id='wp-block-heading-inline-css'>
        h1.has-background,h2.has-background,h3.has-background,h4.has-background,h5.has-background,h6.has-background{padding:1.25em 2.375em}
        </style>
        <style id='wp-block-post-featured-image-inline-css'>
        .wp-block-post-featured-image{margin-left:0;margin-right:0}.wp-block-post-featured-image a{display:block}.wp-block-post-featured-image img{box-sizing:border-box;height:auto;max-width:100%;vertical-align:bottom;width:100%}.wp-block-post-featured-image.alignfull img,.wp-block-post-featured-image.alignwide img{width:100%}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim{background-color:#000;inset:0;position:absolute}.wp-block-post-featured-image{position:relative}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-gradient{background-color:transparent}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-0{opacity:0}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-10{opacity:.1}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-20{opacity:.2}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-30{opacity:.3}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-40{opacity:.4}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-50{opacity:.5}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-60{opacity:.6}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-70{opacity:.7}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-80{opacity:.8}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-90{opacity:.9}.wp-block-post-featured-image .wp-block-post-featured-image__overlay.has-background-dim-100{opacity:1}
        </style>
        <style id='wp-block-post-title-inline-css'>
        .wp-block-post-title{box-sizing:border-box;word-break:break-word}.wp-block-post-title a{display:inline-block}
        .wp-block-post-title{font-weight: 400;margin-top: 1.25rem;margin-bottom: 1.25rem;}
        .wp-block-post-title a:where(:not(.wp-element-button)){text-decoration: none;}
        .wp-block-post-title a:where(:not(.wp-element-button)):hover{text-decoration: underline;}
        .wp-block-post-title a:where(:not(.wp-element-button)):focus{text-decoration: underline dashed;}
        .wp-block-post-title a:where(:not(.wp-element-button)):active{color: var(--wp--preset--color--secondary);text-decoration: none;}
        </style>
        <style id='wp-block-paragraph-inline-css'>
        .is-small-text{font-size:.875em}.is-regular-text{font-size:1em}.is-large-text{font-size:2.25em}.is-larger-text{font-size:3em}.has-drop-cap:not(:focus):first-letter{float:left;font-size:8.4em;font-style:normal;font-weight:100;line-height:.68;margin:.05em .1em 0 0;text-transform:uppercase}body.rtl .has-drop-cap:not(:focus):first-letter{float:none;margin-left:.1em}p.has-drop-cap.has-background{overflow:hidden}p.has-background{padding:1.25em 2.375em}:where(p.has-text-color:not(.has-link-color)) a{color:inherit}
        </style>
        <style id='wp-block-post-excerpt-inline-css'>
        .wp-block-post-excerpt{margin-bottom:var(--wp--style--block-gap);margin-top:var(--wp--style--block-gap)}.wp-block-post-excerpt__excerpt{margin-bottom:0;margin-top:0}.wp-block-post-excerpt__more-text{margin-bottom:0;margin-top:var(--wp--style--block-gap)}.wp-block-post-excerpt__more-link{display:inline-block}
        .wp-block-post-excerpt{font-size: var(--wp--preset--font-size--medium);}
        </style>
        <style id='wp-block-post-date-inline-css'>
        .wp-block-post-date{box-sizing:border-box}
        .wp-block-post-date{font-size: var(--wp--preset--font-size--small);font-weight: 400;}
        .wp-block-post-date a:where(:not(.wp-element-button)){text-decoration: none;}
        .wp-block-post-date a:where(:not(.wp-element-button)):hover{text-decoration: underline;}
        </style>
        <style id='wp-block-spacer-inline-css'>
        .wp-block-spacer{clear:both}
        </style>
        <style id='wp-block-post-template-inline-css'>
        .wp-block-post-template{list-style:none;margin-bottom:0;margin-top:0;max-width:100%;padding:0}.wp-block-post-template.wp-block-post-template{background:none}.wp-block-post-template.is-flex-container{display:flex;flex-direction:row;flex-wrap:wrap;gap:1.25em}.wp-block-post-template.is-flex-container li{margin:0;width:100%}@media (min-width:600px){.wp-block-post-template.is-flex-container.is-flex-container.columns-2>li{width:calc(50% - .625em)}.wp-block-post-template.is-flex-container.is-flex-container.columns-3>li{width:calc(33.33333% - .83333em)}.wp-block-post-template.is-flex-container.is-flex-container.columns-4>li{width:calc(25% - .9375em)}.wp-block-post-template.is-flex-container.is-flex-container.columns-5>li{width:calc(20% - 1em)}.wp-block-post-template.is-flex-container.is-flex-container.columns-6>li{width:calc(16.66667% - 1.04167em)}}
        </style>
        <style id='wp-block-query-pagination-inline-css'>
        .wp-block-query-pagination>.wp-block-query-pagination-next,.wp-block-query-pagination>.wp-block-query-pagination-numbers,.wp-block-query-pagination>.wp-block-query-pagination-previous{margin-bottom:.5em;margin-right:.5em}.wp-block-query-pagination>.wp-block-query-pagination-next:last-child,.wp-block-query-pagination>.wp-block-query-pagination-numbers:last-child,.wp-block-query-pagination>.wp-block-query-pagination-previous:last-child{margin-right:0}.wp-block-query-pagination.is-content-justification-space-between>.wp-block-query-pagination-next:last-of-type{margin-inline-start:auto}.wp-block-query-pagination.is-content-justification-space-between>.wp-block-query-pagination-previous:first-child{margin-inline-end:auto}.wp-block-query-pagination .wp-block-query-pagination-previous-arrow{display:inline-block;margin-right:1ch}.wp-block-query-pagination .wp-block-query-pagination-previous-arrow:not(.is-arrow-chevron){transform:scaleX(1)}.wp-block-query-pagination .wp-block-query-pagination-next-arrow{display:inline-block;margin-left:1ch}.wp-block-query-pagination .wp-block-query-pagination-next-arrow:not(.is-arrow-chevron){transform:scaleX(1)}.wp-block-query-pagination.aligncenter{justify-content:center}
        .wp-block-query-pagination{font-size: var(--wp--preset--font-size--small);font-weight: 400;}
        .wp-block-query-pagination a:where(:not(.wp-element-button)){text-decoration: none;}
        .wp-block-query-pagination a:where(:not(.wp-element-button)):hover{text-decoration: underline;}
        </style>
        <style id='wp-block-query-inline-css'>
        .wp-block-query h2{font-size: var(--wp--preset--font-size--x-large);}
        </style>
        <style id='wp-block-button-inline-css'>
        .wp-block-button__link{box-sizing:border-box;cursor:pointer;display:inline-block;text-align:center;word-break:break-word}.wp-block-button__link.aligncenter{text-align:center}.wp-block-button__link.alignright{text-align:right}:where(.wp-block-button__link){border-radius:9999px;box-shadow:none;padding:calc(.667em + 2px) calc(1.333em + 2px);text-decoration:none}.wp-block-button[style*=text-decoration] .wp-block-button__link{text-decoration:inherit}.wp-block-buttons>.wp-block-button.has-custom-width{max-width:none}.wp-block-buttons>.wp-block-button.has-custom-width .wp-block-button__link{width:100%}.wp-block-buttons>.wp-block-button.has-custom-font-size .wp-block-button__link{font-size:inherit}.wp-block-buttons>.wp-block-button.wp-block-button__width-25{width:calc(25% - var(--wp--style--block-gap, .5em)*.75)}.wp-block-buttons>.wp-block-button.wp-block-button__width-50{width:calc(50% - var(--wp--style--block-gap, .5em)*.5)}.wp-block-buttons>.wp-block-button.wp-block-button__width-75{width:calc(75% - var(--wp--style--block-gap, .5em)*.25)}.wp-block-buttons>.wp-block-button.wp-block-button__width-100{flex-basis:100%;width:100%}.wp-block-buttons.is-vertical>.wp-block-button.wp-block-button__width-25{width:25%}.wp-block-buttons.is-vertical>.wp-block-button.wp-block-button__width-50{width:50%}.wp-block-buttons.is-vertical>.wp-block-button.wp-block-button__width-75{width:75%}.wp-block-button.is-style-squared,.wp-block-button__link.wp-block-button.is-style-squared{border-radius:0}.wp-block-button.no-border-radius,.wp-block-button__link.no-border-radius{border-radius:0!important}.wp-block-button .wp-block-button__link.is-style-outline,.wp-block-button.is-style-outline>.wp-block-button__link{border:2px solid;padding:.667em 1.333em}.wp-block-button .wp-block-button__link.is-style-outline:not(.has-text-color),.wp-block-button.is-style-outline>.wp-block-button__link:not(.has-text-color){color:currentColor}.wp-block-button .wp-block-button__link.is-style-outline:not(.has-background),.wp-block-button.is-style-outline>.wp-block-button__link:not(.has-background){background-color:transparent;background-image:none}
        </style>
        <style id='wp-block-buttons-inline-css'>
        .wp-block-buttons.is-vertical{flex-direction:column}.wp-block-buttons.is-vertical>.wp-block-button:last-child{margin-bottom:0}.wp-block-buttons>.wp-block-button{display:inline-block;margin:0}.wp-block-buttons.is-content-justification-left{justify-content:flex-start}.wp-block-buttons.is-content-justification-left.is-vertical{align-items:flex-start}.wp-block-buttons.is-content-justification-center{justify-content:center}.wp-block-buttons.is-content-justification-center.is-vertical{align-items:center}.wp-block-buttons.is-content-justification-right{justify-content:flex-end}.wp-block-buttons.is-content-justification-right.is-vertical{align-items:flex-end}.wp-block-buttons.is-content-justification-space-between{justify-content:space-between}.wp-block-buttons.aligncenter{text-align:center}.wp-block-buttons:not(.is-content-justification-space-between,.is-content-justification-right,.is-content-justification-left,.is-content-justification-center) .wp-block-button.aligncenter{margin-left:auto;margin-right:auto;width:100%}.wp-block-buttons[style*=text-decoration] .wp-block-button,.wp-block-buttons[style*=text-decoration] .wp-block-button__link{text-decoration:inherit}.wp-block-buttons.has-custom-font-size .wp-block-button__link{font-size:inherit}.wp-block-button.aligncenter{text-align:center}
        </style>
        <style id='wp-block-separator-inline-css'>
        @charset "UTF-8";.wp-block-separator{border:1px solid;border-left:none;border-right:none}.wp-block-separator.is-style-dots{background:none!important;border:none;height:auto;line-height:1;text-align:center}.wp-block-separator.is-style-dots:before{color:currentColor;content:"···";font-family:serif;font-size:1.5em;letter-spacing:2em;padding-left:2em}
        </style>
        <style id='wp-block-columns-inline-css'>
        .wp-block-columns{align-items:normal!important;box-sizing:border-box;display:flex;flex-wrap:wrap!important;margin-bottom:1.75em}@media (min-width:782px){.wp-block-columns{flex-wrap:nowrap!important}}.wp-block-columns.are-vertically-aligned-top{align-items:flex-start}.wp-block-columns.are-vertically-aligned-center{align-items:center}.wp-block-columns.are-vertically-aligned-bottom{align-items:flex-end}@media (max-width:781px){.wp-block-columns:not(.is-not-stacked-on-mobile)>.wp-block-column{flex-basis:100%!important}}@media (min-width:782px){.wp-block-columns:not(.is-not-stacked-on-mobile)>.wp-block-column{flex-basis:0;flex-grow:1}.wp-block-columns:not(.is-not-stacked-on-mobile)>.wp-block-column[style*=flex-basis]{flex-grow:0}}.wp-block-columns.is-not-stacked-on-mobile{flex-wrap:nowrap!important}.wp-block-columns.is-not-stacked-on-mobile>.wp-block-column{flex-basis:0;flex-grow:1}.wp-block-columns.is-not-stacked-on-mobile>.wp-block-column[style*=flex-basis]{flex-grow:0}:where(.wp-block-columns.has-background){padding:1.25em 2.375em}.wp-block-column{flex-grow:1;min-width:0;overflow-wrap:break-word;word-break:break-word}.wp-block-column.is-vertically-aligned-top{align-self:flex-start}.wp-block-column.is-vertically-aligned-center{align-self:center}.wp-block-column.is-vertically-aligned-bottom{align-self:flex-end}.wp-block-column.is-vertically-aligned-bottom,.wp-block-column.is-vertically-aligned-center,.wp-block-column.is-vertically-aligned-top{width:100%}
        </style>
        <style id='wp-block-library-inline-css'>
        :root{--wp-admin-theme-color:#007cba;--wp-admin-theme-color--rgb:0,124,186;--wp-admin-theme-color-darker-10:#006ba1;--wp-admin-theme-color-darker-10--rgb:0,107,161;--wp-admin-theme-color-darker-20:#005a87;--wp-admin-theme-color-darker-20--rgb:0,90,135;--wp-admin-border-width-focus:2px;--wp-block-synced-color:#7a00df;--wp-block-synced-color--rgb:122,0,223}@media (-webkit-min-device-pixel-ratio:2),(min-resolution:192dpi){:root{--wp-admin-border-width-focus:1.5px}}.wp-element-button{cursor:pointer}:root{--wp--preset--font-size--normal:16px;--wp--preset--font-size--huge:42px}:root .has-very-light-gray-background-color{background-color:#eee}:root .has-very-dark-gray-background-color{background-color:#313131}:root .has-very-light-gray-color{color:#eee}:root .has-very-dark-gray-color{color:#313131}:root .has-vivid-green-cyan-to-vivid-cyan-blue-gradient-background{background:linear-gradient(135deg,#00d084,#0693e3)}:root .has-purple-crush-gradient-background{background:linear-gradient(135deg,#34e2e4,#4721fb 50%,#ab1dfe)}:root .has-hazy-dawn-gradient-background{background:linear-gradient(135deg,#faaca8,#dad0ec)}:root .has-subdued-olive-gradient-background{background:linear-gradient(135deg,#fafae1,#67a671)}:root .has-atomic-cream-gradient-background{background:linear-gradient(135deg,#fdd79a,#004a59)}:root .has-nightshade-gradient-background{background:linear-gradient(135deg,#330968,#31cdcf)}:root .has-midnight-gradient-background{background:linear-gradient(135deg,#020381,#2874fc)}.has-regular-font-size{font-size:1em}.has-larger-font-size{font-size:2.625em}.has-normal-font-size{font-size:var(--wp--preset--font-size--normal)}.has-huge-font-size{font-size:var(--wp--preset--font-size--huge)}.has-text-align-center{text-align:center}.has-text-align-left{text-align:left}.has-text-align-right{text-align:right}#end-resizable-editor-section{display:none}.aligncenter{clear:both}.items-justified-left{justify-content:flex-start}.items-justified-center{justify-content:center}.items-justified-right{justify-content:flex-end}.items-justified-space-between{justify-content:space-between}.screen-reader-text{clip:rect(1px,1px,1px,1px);word-wrap:normal!important;border:0;-webkit-clip-path:inset(50%);clip-path:inset(50%);height:1px;margin:-1px;overflow:hidden;padding:0;position:absolute;width:1px}.screen-reader-text:focus{clip:auto!important;background-color:#ddd;-webkit-clip-path:none;clip-path:none;color:#444;display:block;font-size:1em;height:auto;left:5px;line-height:normal;padding:15px 23px 14px;text-decoration:none;top:5px;width:auto;z-index:100000}html :where(.has-border-color){border-style:solid}html :where([style*=border-top-color]){border-top-style:solid}html :where([style*=border-right-color]){border-right-style:solid}html :where([style*=border-bottom-color]){border-bottom-style:solid}html :where([style*=border-left-color]){border-left-style:solid}html :where([style*=border-width]){border-style:solid}html :where([style*=border-top-width]){border-top-style:solid}html :where([style*=border-right-width]){border-right-style:solid}html :where([style*=border-bottom-width]){border-bottom-style:solid}html :where([style*=border-left-width]){border-left-style:solid}html :where(img[class*=wp-image-]){height:auto;max-width:100%}figure{margin:0 0 1em}html :where(.is-position-sticky){--wp-admin--admin-bar--position-offset:var(--wp-admin--admin-bar--height,0px)}@media screen and (max-width:600px){html :where(.is-position-sticky){--wp-admin--admin-bar--position-offset:0px}}
        </style>
        <style id='global-styles-inline-css'>
        body{--wp--preset--color--black: #000000;--wp--preset--color--cyan-bluish-gray: #abb8c3;--wp--preset--color--white: #ffffff;--wp--preset--color--pale-pink: #f78da7;--wp--preset--color--vivid-red: #cf2e2e;--wp--preset--color--luminous-vivid-orange: #ff6900;--wp--preset--color--luminous-vivid-amber: #fcb900;--wp--preset--color--light-green-cyan: #7bdcb5;--wp--preset--color--vivid-green-cyan: #00d084;--wp--preset--color--pale-cyan-blue: #8ed1fc;--wp--preset--color--vivid-cyan-blue: #0693e3;--wp--preset--color--vivid-purple: #9b51e0;--wp--preset--color--base: #ffffff;--wp--preset--color--contrast: #000000;--wp--preset--color--primary: #9DFF20;--wp--preset--color--secondary: #345C00;--wp--preset--color--tertiary: #F6F6F6;--wp--preset--gradient--vivid-cyan-blue-to-vivid-purple: linear-gradient(135deg,rgba(6,147,227,1) 0%,rgb(155,81,224) 100%);--wp--preset--gradient--light-green-cyan-to-vivid-green-cyan: linear-gradient(135deg,rgb(122,220,180) 0%,rgb(0,208,130) 100%);--wp--preset--gradient--luminous-vivid-amber-to-luminous-vivid-orange: linear-gradient(135deg,rgba(252,185,0,1) 0%,rgba(255,105,0,1) 100%);--wp--preset--gradient--luminous-vivid-orange-to-vivid-red: linear-gradient(135deg,rgba(255,105,0,1) 0%,rgb(207,46,46) 100%);--wp--preset--gradient--very-light-gray-to-cyan-bluish-gray: linear-gradient(135deg,rgb(238,238,238) 0%,rgb(169,184,195) 100%);--wp--preset--gradient--cool-to-warm-spectrum: linear-gradient(135deg,rgb(74,234,220) 0%,rgb(151,120,209) 20%,rgb(207,42,186) 40%,rgb(238,44,130) 60%,rgb(251,105,98) 80%,rgb(254,248,76) 100%);--wp--preset--gradient--blush-light-purple: linear-gradient(135deg,rgb(255,206,236) 0%,rgb(152,150,240) 100%);--wp--preset--gradient--blush-bordeaux: linear-gradient(135deg,rgb(254,205,165) 0%,rgb(254,45,45) 50%,rgb(107,0,62) 100%);--wp--preset--gradient--luminous-dusk: linear-gradient(135deg,rgb(255,203,112) 0%,rgb(199,81,192) 50%,rgb(65,88,208) 100%);--wp--preset--gradient--pale-ocean: linear-gradient(135deg,rgb(255,245,203) 0%,rgb(182,227,212) 50%,rgb(51,167,181) 100%);--wp--preset--gradient--electric-grass: linear-gradient(135deg,rgb(202,248,128) 0%,rgb(113,206,126) 100%);--wp--preset--gradient--midnight: linear-gradient(135deg,rgb(2,3,129) 0%,rgb(40,116,252) 100%);--wp--preset--duotone--dark-grayscale: url('#wp-duotone-dark-grayscale');--wp--preset--duotone--grayscale: url('#wp-duotone-grayscale');--wp--preset--duotone--purple-yellow: url('#wp-duotone-purple-yellow');--wp--preset--duotone--blue-red: url('#wp-duotone-blue-red');--wp--preset--duotone--midnight: url('#wp-duotone-midnight');--wp--preset--duotone--magenta-yellow: url('#wp-duotone-magenta-yellow');--wp--preset--duotone--purple-green: url('#wp-duotone-purple-green');--wp--preset--duotone--blue-orange: url('#wp-duotone-blue-orange');--wp--preset--font-size--small: clamp(0.875rem, 0.875rem + ((1vw - 0.48rem) * 0.24), 1rem);--wp--preset--font-size--medium: clamp(1rem, 1rem + ((1vw - 0.48rem) * 0.24), 1.125rem);--wp--preset--font-size--large: clamp(1.75rem, 1.75rem + ((1vw - 0.48rem) * 0.24), 1.875rem);--wp--preset--font-size--x-large: 2.25rem;--wp--preset--font-size--xx-large: clamp(4rem, 4rem + ((1vw - 0.48rem) * 11.538), 10rem);--wp--preset--font-family--dm-sans: "DM Sans", sans-serif;--wp--preset--font-family--ibm-plex-mono: 'IBM Plex Mono', monospace;--wp--preset--font-family--inter: "Inter", sans-serif;--wp--preset--font-family--system-font: -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Oxygen-Sans,Ubuntu,Cantarell,"Helvetica Neue",sans-serif;--wp--preset--font-family--source-serif-pro: "Source Serif Pro", serif;--wp--preset--spacing--30: clamp(1.5rem, 5vw, 2rem);--wp--preset--spacing--40: clamp(1.8rem, 1.8rem + ((1vw - 0.48rem) * 2.885), 3rem);--wp--preset--spacing--50: clamp(2.5rem, 8vw, 4.5rem);--wp--preset--spacing--60: clamp(3.75rem, 10vw, 7rem);--wp--preset--spacing--70: clamp(5rem, 5.25rem + ((1vw - 0.48rem) * 9.096), 8rem);--wp--preset--spacing--80: clamp(7rem, 14vw, 11rem);--wp--preset--shadow--natural: 6px 6px 9px rgba(0, 0, 0, 0.2);--wp--preset--shadow--deep: 12px 12px 50px rgba(0, 0, 0, 0.4);--wp--preset--shadow--sharp: 6px 6px 0px rgba(0, 0, 0, 0.2);--wp--preset--shadow--outlined: 6px 6px 0px -3px rgba(255, 255, 255, 1), 6px 6px rgba(0, 0, 0, 1);--wp--preset--shadow--crisp: 6px 6px 0px rgba(0, 0, 0, 1);}body { margin: 0;--wp--style--global--content-size: 650px;--wp--style--global--wide-size: 1200px; }.wp-site-blocks { padding-top: var(--wp--style--root--padding-top); padding-bottom: var(--wp--style--root--padding-bottom); }.has-global-padding { padding-right: var(--wp--style--root--padding-right); padding-left: var(--wp--style--root--padding-left); }.has-global-padding :where(.has-global-padding) { padding-right: 0; padding-left: 0; }.has-global-padding > .alignfull { margin-right: calc(var(--wp--style--root--padding-right) * -1); margin-left: calc(var(--wp--style--root--padding-left) * -1); }.has-global-padding :where(.has-global-padding) > .alignfull { margin-right: 0; margin-left: 0; }.has-global-padding > .alignfull:where(:not(.has-global-padding)) > :where([class*="wp-block-"]:not(.alignfull):not([class*="__"]),p,h1,h2,h3,h4,h5,h6,ul,ol) { padding-right: var(--wp--style--root--padding-right); padding-left: var(--wp--style--root--padding-left); }.has-global-padding :where(.has-global-padding) > .alignfull:where(:not(.has-global-padding)) > :where([class*="wp-block-"]:not(.alignfull):not([class*="__"]),p,h1,h2,h3,h4,h5,h6,ul,ol) { padding-right: 0; padding-left: 0; }.wp-site-blocks > .alignleft { float: left; margin-right: 2em; }.wp-site-blocks > .alignright { float: right; margin-left: 2em; }.wp-site-blocks > .aligncenter { justify-content: center; margin-left: auto; margin-right: auto; }.wp-site-blocks > * { margin-block-start: 0; margin-block-end: 0; }.wp-site-blocks > * + * { margin-block-start: 1.5rem; }body { --wp--style--block-gap: 1.5rem; }body .is-layout-flow > *{margin-block-start: 0;margin-block-end: 0;}body .is-layout-flow > * + *{margin-block-start: 1.5rem;margin-block-end: 0;}body .is-layout-constrained > *{margin-block-start: 0;margin-block-end: 0;}body .is-layout-constrained > * + *{margin-block-start: 1.5rem;margin-block-end: 0;}body .is-layout-flex{gap: 1.5rem;}body .is-layout-flow > .alignleft{float: left;margin-inline-start: 0;margin-inline-end: 2em;}body .is-layout-flow > .alignright{float: right;margin-inline-start: 2em;margin-inline-end: 0;}body .is-layout-flow > .aligncenter{margin-left: auto !important;margin-right: auto !important;}body .is-layout-constrained > .alignleft{float: left;margin-inline-start: 0;margin-inline-end: 2em;}body .is-layout-constrained > .alignright{float: right;margin-inline-start: 2em;margin-inline-end: 0;}body .is-layout-constrained > .aligncenter{margin-left: auto !important;margin-right: auto !important;}body .is-layout-constrained > :where(:not(.alignleft):not(.alignright):not(.alignfull)){max-width: var(--wp--style--global--content-size);margin-left: auto !important;margin-right: auto !important;}body .is-layout-constrained > .alignwide{max-width: var(--wp--style--global--wide-size);}body .is-layout-flex{display: flex;}body .is-layout-flex{flex-wrap: wrap;align-items: center;}body .is-layout-flex > *{margin: 0;}body{background-color: var(--wp--preset--color--base);color: var(--wp--preset--color--contrast);font-family: var(--wp--preset--font-family--system-font);font-size: var(--wp--preset--font-size--medium);line-height: 1.6;--wp--style--root--padding-top: var(--wp--preset--spacing--40);--wp--style--root--padding-right: var(--wp--preset--spacing--30);--wp--style--root--padding-bottom: var(--wp--preset--spacing--40);--wp--style--root--padding-left: var(--wp--preset--spacing--30);}a:where(:not(.wp-element-button)){color: var(--wp--preset--color--contrast);text-decoration: underline;}a:where(:not(.wp-element-button)):hover{text-decoration: none;}a:where(:not(.wp-element-button)):focus{text-decoration: underline dashed;}a:where(:not(.wp-element-button)):active{color: var(--wp--preset--color--secondary);text-decoration: none;}h1, h2, h3, h4, h5, h6{font-weight: 400;line-height: 1.4;}h1{font-size: clamp(2.719rem, 2.719rem + ((1vw - 0.48rem) * 1.742), 3.625rem);line-height: 1.2;}h2{font-size: clamp(2.625rem, calc(2.625rem + ((1vw - 0.48rem) * 8.4135)), 3.25rem);line-height: 1.2;}h3{font-size: var(--wp--preset--font-size--x-large);}h4{font-size: var(--wp--preset--font-size--large);}h5{font-size: var(--wp--preset--font-size--medium);font-weight: 700;text-transform: uppercase;}h6{font-size: var(--wp--preset--font-size--medium);text-transform: uppercase;}.wp-element-button, .wp-block-button__link{background-color: var(--wp--preset--color--primary);border-radius: 0;border-width: 0;color: var(--wp--preset--color--contrast);font-family: inherit;font-size: inherit;line-height: inherit;padding: calc(0.667em + 2px) calc(1.333em + 2px);text-decoration: none;}.wp-element-button:visited, .wp-block-button__link:visited{color: var(--wp--preset--color--contrast);}.wp-element-button:hover, .wp-block-button__link:hover{background-color: var(--wp--preset--color--contrast);color: var(--wp--preset--color--base);}.wp-element-button:focus, .wp-block-button__link:focus{background-color: var(--wp--preset--color--contrast);color: var(--wp--preset--color--base);}.wp-element-button:active, .wp-block-button__link:active{background-color: var(--wp--preset--color--secondary);color: var(--wp--preset--color--base);}.has-black-color{color: var(--wp--preset--color--black) !important;}.has-cyan-bluish-gray-color{color: var(--wp--preset--color--cyan-bluish-gray) !important;}.has-white-color{color: var(--wp--preset--color--white) !important;}.has-pale-pink-color{color: var(--wp--preset--color--pale-pink) !important;}.has-vivid-red-color{color: var(--wp--preset--color--vivid-red) !important;}.has-luminous-vivid-orange-color{color: var(--wp--preset--color--luminous-vivid-orange) !important;}.has-luminous-vivid-amber-color{color: var(--wp--preset--color--luminous-vivid-amber) !important;}.has-light-green-cyan-color{color: var(--wp--preset--color--light-green-cyan) !important;}.has-vivid-green-cyan-color{color: var(--wp--preset--color--vivid-green-cyan) !important;}.has-pale-cyan-blue-color{color: var(--wp--preset--color--pale-cyan-blue) !important;}.has-vivid-cyan-blue-color{color: var(--wp--preset--color--vivid-cyan-blue) !important;}.has-vivid-purple-color{color: var(--wp--preset--color--vivid-purple) !important;}.has-base-color{color: var(--wp--preset--color--base) !important;}.has-contrast-color{color: var(--wp--preset--color--contrast) !important;}.has-primary-color{color: var(--wp--preset--color--primary) !important;}.has-secondary-color{color: var(--wp--preset--color--secondary) !important;}.has-tertiary-color{color: var(--wp--preset--color--tertiary) !important;}.has-black-background-color{background-color: var(--wp--preset--color--black) !important;}.has-cyan-bluish-gray-background-color{background-color: var(--wp--preset--color--cyan-bluish-gray) !important;}.has-white-background-color{background-color: var(--wp--preset--color--white) !important;}.has-pale-pink-background-color{background-color: var(--wp--preset--color--pale-pink) !important;}.has-vivid-red-background-color{background-color: var(--wp--preset--color--vivid-red) !important;}.has-luminous-vivid-orange-background-color{background-color: var(--wp--preset--color--luminous-vivid-orange) !important;}.has-luminous-vivid-amber-background-color{background-color: var(--wp--preset--color--luminous-vivid-amber) !important;}.has-light-green-cyan-background-color{background-color: var(--wp--preset--color--light-green-cyan) !important;}.has-vivid-green-cyan-background-color{background-color: var(--wp--preset--color--vivid-green-cyan) !important;}.has-pale-cyan-blue-background-color{background-color: var(--wp--preset--color--pale-cyan-blue) !important;}.has-vivid-cyan-blue-background-color{background-color: var(--wp--preset--color--vivid-cyan-blue) !important;}.has-vivid-purple-background-color{background-color: var(--wp--preset--color--vivid-purple) !important;}.has-base-background-color{background-color: var(--wp--preset--color--base) !important;}.has-contrast-background-color{background-color: var(--wp--preset--color--contrast) !important;}.has-primary-background-color{background-color: var(--wp--preset--color--primary) !important;}.has-secondary-background-color{background-color: var(--wp--preset--color--secondary) !important;}.has-tertiary-background-color{background-color: var(--wp--preset--color--tertiary) !important;}.has-black-border-color{border-color: var(--wp--preset--color--black) !important;}.has-cyan-bluish-gray-border-color{border-color: var(--wp--preset--color--cyan-bluish-gray) !important;}.has-white-border-color{border-color: var(--wp--preset--color--white) !important;}.has-pale-pink-border-color{border-color: var(--wp--preset--color--pale-pink) !important;}.has-vivid-red-border-color{border-color: var(--wp--preset--color--vivid-red) !important;}.has-luminous-vivid-orange-border-color{border-color: var(--wp--preset--color--luminous-vivid-orange) !important;}.has-luminous-vivid-amber-border-color{border-color: var(--wp--preset--color--luminous-vivid-amber) !important;}.has-light-green-cyan-border-color{border-color: var(--wp--preset--color--light-green-cyan) !important;}.has-vivid-green-cyan-border-color{border-color: var(--wp--preset--color--vivid-green-cyan) !important;}.has-pale-cyan-blue-border-color{border-color: var(--wp--preset--color--pale-cyan-blue) !important;}.has-vivid-cyan-blue-border-color{border-color: var(--wp--preset--color--vivid-cyan-blue) !important;}.has-vivid-purple-border-color{border-color: var(--wp--preset--color--vivid-purple) !important;}.has-base-border-color{border-color: var(--wp--preset--color--base) !important;}.has-contrast-border-color{border-color: var(--wp--preset--color--contrast) !important;}.has-primary-border-color{border-color: var(--wp--preset--color--primary) !important;}.has-secondary-border-color{border-color: var(--wp--preset--color--secondary) !important;}.has-tertiary-border-color{border-color: var(--wp--preset--color--tertiary) !important;}.has-vivid-cyan-blue-to-vivid-purple-gradient-background{background: var(--wp--preset--gradient--vivid-cyan-blue-to-vivid-purple) !important;}.has-light-green-cyan-to-vivid-green-cyan-gradient-background{background: var(--wp--preset--gradient--light-green-cyan-to-vivid-green-cyan) !important;}.has-luminous-vivid-amber-to-luminous-vivid-orange-gradient-background{background: var(--wp--preset--gradient--luminous-vivid-amber-to-luminous-vivid-orange) !important;}.has-luminous-vivid-orange-to-vivid-red-gradient-background{background: var(--wp--preset--gradient--luminous-vivid-orange-to-vivid-red) !important;}.has-very-light-gray-to-cyan-bluish-gray-gradient-background{background: var(--wp--preset--gradient--very-light-gray-to-cyan-bluish-gray) !important;}.has-cool-to-warm-spectrum-gradient-background{background: var(--wp--preset--gradient--cool-to-warm-spectrum) !important;}.has-blush-light-purple-gradient-background{background: var(--wp--preset--gradient--blush-light-purple) !important;}.has-blush-bordeaux-gradient-background{background: var(--wp--preset--gradient--blush-bordeaux) !important;}.has-luminous-dusk-gradient-background{background: var(--wp--preset--gradient--luminous-dusk) !important;}.has-pale-ocean-gradient-background{background: var(--wp--preset--gradient--pale-ocean) !important;}.has-electric-grass-gradient-background{background: var(--wp--preset--gradient--electric-grass) !important;}.has-midnight-gradient-background{background: var(--wp--preset--gradient--midnight) !important;}.has-small-font-size{font-size: var(--wp--preset--font-size--small) !important;}.has-medium-font-size{font-size: var(--wp--preset--font-size--medium) !important;}.has-large-font-size{font-size: var(--wp--preset--font-size--large) !important;}.has-x-large-font-size{font-size: var(--wp--preset--font-size--x-large) !important;}.has-xx-large-font-size{font-size: var(--wp--preset--font-size--xx-large) !important;}.has-dm-sans-font-family{font-family: var(--wp--preset--font-family--dm-sans) !important;}.has-ibm-plex-mono-font-family{font-family: var(--wp--preset--font-family--ibm-plex-mono) !important;}.has-inter-font-family{font-family: var(--wp--preset--font-family--inter) !important;}.has-system-font-font-family{font-family: var(--wp--preset--font-family--system-font) !important;}.has-source-serif-pro-font-family{font-family: var(--wp--preset--font-family--source-serif-pro) !important;}
        </style>
        <style id='core-block-supports-inline-css'>
        .wp-container-2.wp-container-2{justify-content:flex-end;}.wp-container-11.wp-container-11{flex-wrap:nowrap;}.wp-container-3.wp-container-3,.wp-container-6.wp-container-6,.wp-container-13.wp-container-13{justify-content:space-between;}
        </style>
        <style id='wp-webfonts-inline-css'>
        @font-face{font-family:"DM Sans";font-style:normal;font-weight:400;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/dm-sans/DMSans-Regular.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"DM Sans";font-style:italic;font-weight:400;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/dm-sans/DMSans-Regular-Italic.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"DM Sans";font-style:normal;font-weight:700;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/dm-sans/DMSans-Bold.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"DM Sans";font-style:italic;font-weight:700;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/dm-sans/DMSans-Bold-Italic.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"IBM Plex Mono";font-style:normal;font-weight:300;font-display:block;src:url('/wp-content/themes/twentytwentythree/assets/fonts/ibm-plex-mono/IBMPlexMono-Light.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"IBM Plex Mono";font-style:normal;font-weight:400;font-display:block;src:url('/wp-content/themes/twentytwentythree/assets/fonts/ibm-plex-mono/IBMPlexMono-Regular.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"IBM Plex Mono";font-style:italic;font-weight:400;font-display:block;src:url('/wp-content/themes/twentytwentythree/assets/fonts/ibm-plex-mono/IBMPlexMono-Italic.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"IBM Plex Mono";font-style:normal;font-weight:700;font-display:block;src:url('/wp-content/themes/twentytwentythree/assets/fonts/ibm-plex-mono/IBMPlexMono-Bold.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:Inter;font-style:normal;font-weight:200 900;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/inter/Inter-VariableFont_slnt,wght.ttf') format('truetype');font-stretch:normal;}@font-face{font-family:"Source Serif Pro";font-style:normal;font-weight:200 900;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/source-serif-pro/SourceSerif4Variable-Roman.ttf.woff2') format('woff2');font-stretch:normal;}@font-face{font-family:"Source Serif Pro";font-style:italic;font-weight:200 900;font-display:fallback;src:url('/wp-content/themes/twentytwentythree/assets/fonts/source-serif-pro/SourceSerif4Variable-Italic.ttf.woff2') format('woff2');font-stretch:normal;}
        </style>
        <script src='http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-includes/blocks/navigation/view.min.js?ver=c24330f635f5cb9d5e0e' id='wp-block-navigation-view-js'></script>
        <script src='http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-includes/blocks/navigation/view-modal.min.js?ver=f51363b18f0497ec84da' id='wp-block-navigation-view-2-js'></script>
        <link rel="https://api.w.org/" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-json/" /><link rel="EditURI" type="application/rsd+xml" title="RSD" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/xmlrpc.php?rsd" />
        <link rel="wlwmanifest" type="application/wlwmanifest+xml" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/wp-includes/wlwmanifest.xml" />
        <meta name="generator" content="WordPress 6.2" />
        </head>

        <body class="home blog wp-embed-responsive">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-dark-grayscale"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0 0.49803921568627" /><feFuncG type="table" tableValues="0 0.49803921568627" /><feFuncB type="table" tableValues="0 0.49803921568627" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-grayscale"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0 1" /><feFuncG type="table" tableValues="0 1" /><feFuncB type="table" tableValues="0 1" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-purple-yellow"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0.54901960784314 0.98823529411765" /><feFuncG type="table" tableValues="0 1" /><feFuncB type="table" tableValues="0.71764705882353 0.25490196078431" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-blue-red"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0 1" /><feFuncG type="table" tableValues="0 0.27843137254902" /><feFuncB type="table" tableValues="0.5921568627451 0.27843137254902" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-midnight"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0 0" /><feFuncG type="table" tableValues="0 0.64705882352941" /><feFuncB type="table" tableValues="0 1" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-magenta-yellow"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0.78039215686275 1" /><feFuncG type="table" tableValues="0 0.94901960784314" /><feFuncB type="table" tableValues="0.35294117647059 0.47058823529412" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-purple-green"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0.65098039215686 0.40392156862745" /><feFuncG type="table" tableValues="0 1" /><feFuncB type="table" tableValues="0.44705882352941 0.4" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 0 0" width="0" height="0" focusable="false" role="none" style="visibility: hidden; position: absolute; left: -9999px; overflow: hidden;" ><defs><filter id="wp-duotone-blue-orange"><feColorMatrix color-interpolation-filters="sRGB" type="matrix" values=" .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 .299 .587 .114 0 0 " /><feComponentTransfer color-interpolation-filters="sRGB" ><feFuncR type="table" tableValues="0.098039215686275 1" /><feFuncG type="table" tableValues="0 0.66274509803922" /><feFuncB type="table" tableValues="0.84705882352941 0.41960784313725" /><feFuncA type="table" tableValues="1 1" /></feComponentTransfer><feComposite in2="SourceGraphic" operator="in" /></filter></defs></svg>
        <div class="wp-site-blocks"><header class="wp-block-template-part">
        <div class="wp-block-group has-global-padding is-layout-constrained">
                
                <div class="wp-block-group alignwide is-content-justification-space-between is-layout-flex wp-container-3" style="padding-bottom:var(--wp--preset--spacing--40)">
                        <p class="wp-block-site-title"><a href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local" target="_self" rel="home" aria-current="page">User&#039;s Blog!</a></p>
                        <nav class="is-responsive items-justified-right is-fallback wp-block-navigation is-content-justification-right is-layout-flex wp-container-2" aria-label=""><button aria-haspopup="true" aria-label="Open menu" class="wp-block-navigation__responsive-container-open " data-micromodal-trigger="modal-1"><svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" aria-hidden="true" focusable="false"><rect x="4" y="7.5" width="16" height="1.5" /><rect x="4" y="15" width="16" height="1.5" /></svg></button>
                                <div class="wp-block-navigation__responsive-container  " style="" id="modal-1">
                                        <div class="wp-block-navigation__responsive-close" tabindex="-1" data-micromodal-close>
                                                <div class="wp-block-navigation__responsive-dialog" aria-label="Menu">
                                                                <button aria-label="Close menu" data-micromodal-close class="wp-block-navigation__responsive-container-close"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" aria-hidden="true" focusable="false"><path d="M13 11.8l6.1-6.3-1-1-6.1 6.2-6.1-6.2-1 1 6.1 6.3-6.5 6.7 1 1 6.5-6.6 6.5 6.6 1-1z"></path></svg></button>
                                                        <div class="wp-block-navigation__responsive-container-content" id="modal-1-content">
                                                                <ul class="wp-block-page-list"><li class="wp-block-pages-list__item wp-block-navigation-item open-on-hover-click"><a class="wp-block-pages-list__item__link wp-block-navigation-item__content" href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/sample-page/">Sample Page</a></li></ul>
                                                        </div>
                                                </div>
                                        </div>
                                </div></nav>
                </div>
                
        </div>

        </header>


        <main class="wp-block-group has-global-padding is-layout-constrained" style="margin-top:var(--wp--preset--spacing--50);margin-bottom:var(--wp--preset--spacing--70)">
                
                <h1 class="alignwide wp-block-heading" style="margin-bottom:var(--wp--preset--spacing--60)">Mindblown: a blog about philosophy.</h1>
                

                
                <div class="wp-block-query alignwide has-global-padding is-layout-constrained">
                        <ul class="is-flex-container columns-3 alignwide wp-block-post-template is-layout-flow"><li class="wp-block-post post-1 post type-post status-publish format-standard hentry category-uncategorized">
                                
                                <h2 class="wp-block-post-title"><a href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/2023/05/16/hello-world/" target="_self" >Hello world!</a></h2>
                                <div class="wp-block-post-excerpt"><p class="wp-block-post-excerpt__excerpt">Welcome to WordPress. This is your first post. Edit or delete it, then start writing! </p></div>
                                <div class="wp-block-post-date"><time datetime="2023-05-16T00:56:21+00:00"><a href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local/2023/05/16/hello-world/">May 16, 2023</a></time></div>

                                
                                <div style="height:var(--wp--preset--spacing--40)" aria-hidden="true" class="wp-block-spacer"></div>
                                
                        </li></ul>

                        
                </div>
                

                
                <div style="height:var(--wp--preset--spacing--60)" aria-hidden="true" class="wp-block-spacer"></div>
                

                
        <div class="wp-block-columns alignwide is-layout-flex wp-container-11">
                
                <div class="wp-block-column is-layout-flow">
                        
                        <p class="has-x-large-font-size" style="line-height:1.2">Got any book recommendations?		</p>
                        

                        
                        <div class="wp-block-buttons is-layout-flex">
                                
                                <div class="wp-block-button has-custom-font-size has-small-font-size">
                                        <a class="wp-block-button__link wp-element-button">
                                        Get In Touch				</a>
                                </div>
                                
                        </div>
                        
                </div>
                

                
                <div class="wp-block-column is-layout-flow">
                        
                        <hr class="wp-block-separator has-alpha-channel-opacity"/>
                        
                </div>
                
        </div>


        </main>


        <footer class="wp-block-template-part">
        <div class="wp-block-group has-global-padding is-layout-constrained">
                
                <div class="wp-block-group alignwide is-content-justification-space-between is-layout-flex wp-container-13" style="padding-top:var(--wp--preset--spacing--40)">
                        <p class="wp-block-site-title"><a href="http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local" target="_self" rel="home" aria-current="page">User&#039;s Blog!</a></p>
                        
                        <p class="has-text-align-right">
                        Proudly powered by <a href="https://wordpress.org" rel="nofollow">WordPress</a>		</p>
                        
                </div>
                
        </div>


        </footer>
        </div>

                        <style id="skip-link-styles">
                        .skip-link.screen-reader-text {
                                border: 0;
                                clip: rect(1px,1px,1px,1px);
                                clip-path: inset(50%);
                                height: 1px;
                                margin: -1px;
                                overflow: hidden;
                                padding: 0;
                                position: absolute !important;
                                width: 1px;
                                word-wrap: normal !important;
                        }

                        .skip-link.screen-reader-text:focus {
                                background-color: #eee;
                                clip: auto !important;
                                clip-path: none;
                                color: #444;
                                display: block;
                                font-size: 1em;
                                height: auto;
                                left: 5px;
                                line-height: normal;
                                padding: 15px 23px 14px;
                                text-decoration: none;
                                top: 5px;
                                width: auto;
                                z-index: 100000;
                        }
                </style>
                        <script>
                ( function() {
                        var skipLinkTarget = document.querySelector( 'main' ),
                                sibling,
                                skipLinkTargetID,
                                skipLink;

                        // Early exit if a skip-link target can't be located.
                        if ( ! skipLinkTarget ) {
                                return;
                        }

                        // Get the site wrapper.
                        // The skip-link will be injected in the beginning of it.
                        sibling = document.querySelector( '.wp-site-blocks' );

                        // Early exit if the root element was not found.
                        if ( ! sibling ) {
                                return;
                        }

                        // Get the skip-link target's ID, and generate one if it doesn't exist.
                        skipLinkTargetID = skipLinkTarget.id;
                        if ( ! skipLinkTargetID ) {
                                skipLinkTargetID = 'wp--skip-link--target';
                                skipLinkTarget.id = skipLinkTargetID;
                        }

                        // Create the skip link.
                        skipLink = document.createElement( 'a' );
                        skipLink.classList.add( 'skip-link', 'screen-reader-text' );
                        skipLink.href = '#' + skipLinkTargetID;
                        skipLink.innerHTML = 'Skip to content';

                        // Inject the skip link.
                        sibling.parentElement.insert
        100 53313    0 53313    0     0   469k      0 --:--:-- --:--:-- --:--:--  469k
        * Connection #0 to host final-project-wp-scalefocus-wordpress.wp.svc.cluster.local left intact
        Before( skipLink, sibling );
                }() );
                </script>
                </body>
        </html>
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] }
        [Pipeline] // withEnv
        [Pipeline] }
        [Pipeline] // node
        [Pipeline] End of Pipeline
        Finished: SUCCESS


Create port forwarding

        kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 8000:80

![Deplyoment](./assets/images/33.png)
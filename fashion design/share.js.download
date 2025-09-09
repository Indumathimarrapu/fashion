(function () {
    document.addEventListener('DOMContentLoaded', function insertDesignedByWatermark() {
        const shareKey = window.shareKey;
        const host = window.host;
        const apiUrl = `https://${host}/api/public/user/is_free`;
        const readdyLogo = 'https://public.readdy.ai/gen_page/readdy-logo.png';
        const watermarkUrl = 'https://public.readdy.ai/gen_page/watermark.png';
        posthog.capture('sharePageVisited', { shareKey })
        function checkIfPaidUser(shareKey) {
            return fetch(`${apiUrl}?shareKey=${shareKey}`, {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json'
                }
            })
                .then(response => response.json())
                .then(res => {
                    return !res.data.is_free;
                })
                .catch(error => {
                    return false;
                });
        }

        function setFavicon() {
            var existingFavicon = document.querySelector('link[rel="icon"]');
            if (existingFavicon) {
                document.head.removeChild(existingFavicon);
            }
            var link = document.createElement('link');
            link.type = 'image/png';
            link.rel = 'icon';
            link.href = readdyLogo;
            document.head.appendChild(link);
            var fontLink = document.createElement('link');
            fontLink.rel = 'stylesheet';
            fontLink.href = 'https://fonts.googleapis.com/css2?family=Secular+One&display=swap';
            document.head.appendChild(fontLink);
        }

        function createWatermark() {
            const isPC = window.innerWidth > 768 && !('ontouchstart' in window);
            const group = document.createElement('div');
            group.id = 'watermark';
            group.className = 'group';
            group.style.position = 'fixed';
            group.style.bottom = '48px';
            group.style.right = '46px';
            group.style.width = isPC ? '184px' : '114px';
            group.style.height = isPC ? '39px' : '36px';
            group.style.backgroundColor = '#F0F0F0';
            group.style.borderStyle = "solid";
            group.style.borderWidth = "3px";
            group.style.borderRadius = "12px";
            group.style.borderImageSlice = "3";
            group.style.borderImageWidth = "3px";
            group.style.borderImageOutset = "0";
            group.style.borderColor = "#e5e7eb";
            group.style.display = "flex";
            group.style.alignItems = "center";
            group.style.justifyContent = "center";
            const designedBy = document.createElement('div');
            designedBy.className = 'designed-by';
            designedBy.textContent = 'Designed by';
            designedBy.style.color = '#5D5D5D';
            designedBy.style.fontFamily = '"Secular One"';
            designedBy.style.fontSize = '12px';
            designedBy.style.lineHeight = '20px';
            designedBy.style.fontWeight = '400';
            designedBy.style.paddingLeft = isPC ? '12px' : '0';
            group.style.cursor = 'pointer';
            group.addEventListener('click', () => {
                posthog.capture('watermarkClick', { shareKey });
                // window.open('https://{{.Host}}');
                window.open(`https://${host}`);
            });
            const img = document.createElement('img');
            img.src = isPC ? watermarkUrl : readdyLogo;
            img.style.width = isPC ? '86px' : '16px';
            img.style.marginLeft = isPC ? '0' : '6px';
            group.appendChild(designedBy);
            group.appendChild(img);
            return group;
        }
        let paidUser = true;
        setFavicon();
        function handleWaterMark(isPay) {
            if (!document.getElementById('watermark') && !isPay) {
                document.body.appendChild(createWatermark());
            } else if (isPay && document.getElementById('watermark')) {
                document.getElementById('watermark').remove();
            }
        }
        checkIfPaidUser(shareKey).then(isPaidUser => {
            paidUser = isPaidUser;
            handleWaterMark(isPaidUser);
        });
        const observer = new MutationObserver(() => {
            handleWaterMark(paidUser);
        });
        observer.observe(document.body, { childList: true, subtree: true });
    });
})();
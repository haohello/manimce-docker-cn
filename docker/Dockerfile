FROM bitnami/python:3.9

# 更改国内源
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak
COPY docker/sources.list /etc/apt/
COPY docker/pip.conf /etc/
    # echo "deb http://mirrors.cloud.tencent.com/debian/ buster main non-free contrib" >/etc/apt/sources.list && \
    # echo "deb http://mirrors.cloud.tencent.com/debian-security buster/updates main" >>/etc/apt/sources.list && \
    # echo "deb http://mirrors.cloud.tencent.com/debian/ buster-updates main non-free contrib" >>/etc/apt/sources.list && \
    # echo "deb http://mirrors.cloud.tencent.com/debian/ buster-backports main non-free contrib" >>/etc/apt/sources.list \
    # echo "deb-src http://mirrors.cloud.tencent.com/debian-security buster/updates main" >/etc/apt/sources.list && \
    # echo "deb-src http://mirrors.cloud.tencent.com/debian/ buster main non-free contrib" >>/etc/apt/sources.list && \
    # echo "deb-src http://mirrors.cloud.tencent.com/debian/ buster-updates main non-free contrib" >>/etc/apt/sources.list && \
    # echo "deb-src http://mirrors.cloud.tencent.com/debian/ buster-backports main non-free contrib" >>/etc/apt/sources.list

# -qq 忽略日志 只在发生错误时打印
# RUN apt-get -qq update && apt-get install -qq sudo

# 给debian设置中文支持
RUN apt-get -qq update && apt-get install -y locales locales-all
# RUN apt-get install -y fontconfig
# RUN apt-get install -y ttf-mscorefonts-installer
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
ENV LANG zh_CN.utf8

# 更改时区
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone

# 拷贝所有中文字体
COPY docker/fonts/* /usr/share/fonts/

RUN apt-get update -qq \
    && apt-get install --no-install-recommends -y \
        ffmpeg \
        gcc \
        libcairo2-dev \
        libffi-dev \
        libpango1.0-dev \
        pkg-config \
        make \
        wget

# setup full texlive installation
# COPY docker/texlive-profile.txt /tmp/
# ENV PATH=/usr/local/texlive/bin/x86_64-linux:$PATH
# RUN wget -O /tmp/install-tl-unx.tar.gz https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/install-tl-unx.tar.gz && \
#     mkdir /tmp/install-tl && \
#     tar -xzf /tmp/install-tl-unx.tar.gz -C /tmp/install-tl --strip-components=1 && \
#     /tmp/install-tl/install-tl --profile=/tmp/texlive-profile.txt
#     # && tlmgr install \
#     #     amsmath babel-english cbfonts-fd cm-super ctex doublestroke dvisvgm everysel \
#     #     fontspec frcursive fundus-calligra gnu-freefont jknapltx latex-bin \
#     #     mathastext microtype ms physics preview ragged2e relsize rsfs \
#     #     setspace standalone tipa wasy wasysym xcolor xetex xkeyval

# RUN tlmgr option repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/
# RUN tlmgr update --self --all
# RUN tlmgr install scheme-full

# 代码取自 https://github.com/thomasWeise/docker-texlive-full/blob/master/image/Dockerfile
RUN echo "Texlive-full install Initial update." &&\
    apt-get update &&\
# prevent doc and man pages from being installed
# the idea is based on https://askubuntu.com/questions/129566
    echo "Preventing doc and man pages from being installed." &&\
    printf 'path-exclude /usr/share/doc/*\npath-include /usr/share/doc/*/copyright\npath-exclude /usr/share/man/*\npath-exclude /usr/share/groff/*\npath-exclude /usr/share/info/*\npath-exclude /usr/share/lintian/*\npath-exclude /usr/share/linda/*\npath-exclude=/usr/share/locale/*' > /etc/dpkg/dpkg.cfg.d/01_nodoc &&\
# remove doc files and man pages already installed
    rm -rf /usr/share/groff/* /usr/share/info/* &&\
    rm -rf /usr/share/lintian/* /usr/share/linda/* /var/cache/man/* &&\
    rm -rf /usr/share/man &&\
    mkdir -p /usr/share/man &&\
    find /usr/share/doc -depth -type f ! -name copyright -delete &&\
    find /usr/share/doc -type f -name "*.pdf" -delete &&\
    find /usr/share/doc -type f -name "*.gz" -delete &&\
    find /usr/share/doc -type f -name "*.tex" -delete &&\
    (find /usr/share/doc -type d -empty -delete || true) &&\
    mkdir -p /usr/share/doc &&\
    mkdir -p /usr/share/info &&\
# install utilities
    echo "Installing utilities." &&\
    apt-get install -f -y --no-install-recommends apt-utils &&\
# get and update certificates, to hopefully resolve mscorefonts error
    echo "Get and update certificates for mscorefonts." &&\
    apt-get install -f -y --no-install-recommends ca-certificates &&\
    update-ca-certificates &&\
# install some utilitites and nice fonts, e.g., for Chinese and others
    echo "Installing utilities and nice fonts for Chinese and others." &&\
    apt-get install -f -y --no-install-recommends \
          curl \
          emacs-intl-fonts \
          fontconfig \
          fonts-arphic-bkai00mp \
          fonts-arphic-bsmi00lp \
          fonts-arphic-gbsn00lp \
          fonts-arphic-gkai00mp \
          fonts-arphic-ukai \
          fonts-arphic-uming \
          fonts-dejavu \
          fonts-dejavu-core \
          fonts-dejavu-extra \
          fonts-droid-fallback \
          fonts-guru \
          fonts-guru-extra \
          fonts-liberation \
          fonts-noto-cjk \
          fonts-opensymbol \
          fonts-roboto \
          fonts-roboto-hinted \
          fonts-stix \
          fonts-symbola \
          fonts-texgyre \
          fonts-unfonts-core \
          ttf-wqy-microhei \
          ttf-wqy-zenhei \
          xfonts-intl-chinese \
          xfonts-intl-chinese-big \
          xfonts-wqy &&\
# now the microsoft core fonts
    echo "Installing microsoft core fonts." &&\
    echo "ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true" | debconf-set-selections &&\
    echo "ttf-mscorefonts-installer msttcorefonts/present-mscorefonts-eula note" | debconf-set-selections &&\
    curl --output "/tmp/ttf-mscorefonts-installer.deb" "https://mirrors.tuna.tsinghua.edu.cn/debian/pool/contrib/m/msttcorefonts/ttf-mscorefonts-installer_3.8_all.deb" &&\
    (apt install -f -y --no-install-recommends "/tmp/ttf-mscorefonts-installer.deb" || true) &&\
    rm -f "/tmp/ttf-mscorefonts-installer.deb" &&\
# we make sure to contain the EULA in our container
    curl --output "/root/mscorefonts-eula" "http://corefonts.sourceforge.net/eula.htm" &&\
    echo "Installing TeX Live and ghostscript and other tools." &&\
# install TeX Live and ghostscript as well as other tools
    apt-get install -f -y --no-install-recommends \
          biber \
          cm-super \
          dvipng \
          fonts-dejavu \
          fonts-dejavu-core \
          fonts-dejavu-extra \
          ghostscript \
          gnuplot \
          make \
          latex-cjk-all \
          latex-cjk-common \
          latex-cjk-chinese \
          latexmk \
          lcdf-typetools \
          lmodern \
          poppler-utils \
          psutils \
          purifyeps \
          python3-pygments \
          t1utils \
          tex-gyre \
          tex4ht \
          texlive-base \
          texlive-bibtex-extra \
          texlive-binaries \
          texlive-extra-utils \
          texlive-font-utils \
          texlive-fonts-extra \
          texlive-fonts-extra-links \
          texlive-fonts-recommended \
          texlive-formats-extra \
          texlive-lang-all \
          texlive-lang-chinese \
          texlive-lang-cjk \
          texlive-latex-base \
          texlive-latex-extra \
          texlive-latex-recommended \
          texlive-luatex \
          texlive-metapost \
          texlive-pictures \
          texlive-plain-generic \
          texlive-pstricks \
          texlive-publishers \
          texlive-science \
          texlive-xetex \
          texlive-bibtex-extra &&\
# delete Tex Live sources and other potentially useless stuff
    echo "Delete TeX Live sources and other useless stuff." &&\
    (rm -rf /usr/share/texmf/source || true) &&\
    (rm -rf /usr/share/texlive/texmf-dist/source || true) &&\
    find /usr/share/texlive -type f -name "readme*.*" -delete &&\
    find /usr/share/texlive -type f -name "README*.*" -delete &&\
    (rm -rf /usr/share/texlive/release-texlive.txt || true) &&\
    (rm -rf /usr/share/texlive/doc.html || true) &&\
    (rm -rf /usr/share/texlive/index.html || true) &&\
# update font cache
    echo "Update font cache." &&\
    fc-cache -fv &&\
# clean up all temporary files
    echo "Clean up all temporary files." &&\
    apt-get clean -y &&\
    rm -rf /var/lib/apt/lists/* &&\
    rm -f /etc/ssh/ssh_host_* &&\
# delete man pages and documentation
    echo "Delete man pages and documentation." &&\
    rm -rf /usr/share/man &&\
    mkdir -p /usr/share/man &&\
    find /usr/share/doc -depth -type f ! -name copyright -delete &&\
    find /usr/share/doc -type f -name "*.pdf" -delete &&\
    find /usr/share/doc -type f -name "*.gz" -delete &&\
    find /usr/share/doc -type f -name "*.tex" -delete &&\
    (find /usr/share/doc -type d -empty -delete || true) &&\
    mkdir -p /usr/share/doc &&\
    rm -rf /var/cache/apt/archives &&\
    mkdir -p /var/cache/apt/archives &&\
    rm -rf /tmp/* /var/tmp/* &&\
    (find /usr/share/ -type f -empty -delete || true) &&\
    (find /usr/share/ -type d -empty -delete || true) &&\
    mkdir -p /usr/share/texmf/source &&\
    mkdir -p /usr/share/texlive/texmf-dist/source &&\
    echo "Texlive-full All done."

# clone and build manim
COPY . /opt/manim
WORKDIR /opt/manim
# RUN pip install --no-cache jupyterlab webgl_renderer

RUN pip install -r docs/requirements.txt

ARG NB_USER=musr
ARG NB_UID=1000
ENV USER ${NB_USER}
ENV NB_UID ${NB_UID}
ENV HOME /manim

RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

# create working directory for user to mount local directory into
WORKDIR ${HOME}
USER root
RUN chown -R ${NB_USER}:${NB_USER} ${HOME}
RUN chmod 777 ${HOME}
USER ${NB_USER}

CMD [ "/bin/bash" ]
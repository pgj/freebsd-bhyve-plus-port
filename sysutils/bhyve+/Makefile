PORTNAME=	bhyve+
PORTVERSION=	0.1.0
CATEGORIES=	sysutils

MAINTAINER=	pali.gabor@gmail.com
COMMENT=	BHyVe with unofficial extensions

LICENSE=	BSD2CLAUSE

ONLY_FOR_ARCHS=	amd64
USES=		kmod uidfix

.include <bsd.port.options.mk>

SRCS=		${SRC_BASE}/sys/modules/vmm \
		${SRC_BASE}/sys/amd64/vmm \
		${SRC_BASE}/lib/libvmmapi \
		${SRC_BASE}/usr.sbin/bhyve \
		${SRC_BASE}/usr.sbin/bhyvectl \
		${SRC_BASE}/usr.sbin/bhyveload

.for _src in ${SRCS}
.  if !exists(${_src})
IGNORE=		requires bhyve and vmm source files in ${_src}
.  endif
.endfor

.if ${OPSYS} == FreeBSD && ${OSVERSION} > 1300094
HAS_LIB9P=	yes
PLIST_SUB+=	LIB9P="@comment "
.else
PLIST_SUB+=	LIB9P=""
.endif

.if !defined(HAS_LIB9P)
USE_GITHUB=	yes
GH_ACCOUNT=	conclusiveeng
GH_PROJECT=	lib9p
GH_TAGNAME=	9d5aee77bcc1bf0e79b0a3bfefff5fdf2146283c

USE_LDCONFIG=	yes

.if 1103000 <= ${OSVERSION} && ${OSVERSION} < 1200000
EXTRA_PATCHES=	${PATCHDIR}/extra-patch-virtio-9p-freebsd-11
.elif 1201000 <= ${OSVERSION} && ${OSVERSION} < 1300000
# The Ports Framework does not support alternative patches,
# so this workaround is needed.
post-patch:
	@(${ECHO} "===>  Applying extra patches for FreeBSD 12")
	@(if (${CAT} ${PATCHDIR}/extra-patch-virtio-9p-freebsd-12 | ${PATCH} --dry-run --quiet ${PATCH_ARGS} -d ${PATCH_WRKSRC} > /dev/null 2>&1); then \
		${CAT} ${PATCHDIR}/extra-patch-virtio-9p-freebsd-12 | ${PATCH} ${PATCH_ARGS} -d ${PATCH_WRKSRC}; \
	else \
		${CAT} ${PATCHDIR}/extra-patch-virtio-9p-freebsd-12-alt | ${PATCH} ${PATCH_ARGS} -d ${PATCH_WRKSRC}; \
	fi)
.else
IGNORE=		unsupported system
.endif
.else # HAS_LIB9P
EXTRA_PATCHES=	${PATCHDIR}/extra-patch-freebsd-with-lib9p
DISTFILES=	# no distfiles in this case
.endif # HAS_LIB9P

WRKSRC=		${WRKDIR}

_LINKS=		stand/userboot \
		share/mk

post-extract:
.if !defined(HAS_LIB9P)
	@${MKDIR} -p ${WRKSRC}/contrib
	@cd ${WRKSRC} && ${LN} -s ../${GH_PROJECT}-${GH_TAGNAME} contrib/lib9p
.endif
.for _link in ${_LINKS}
	@${MKDIR} -p $$(dirname ${WRKSRC}/${_link})
	@cd ${WRKSRC} && ${LN} -s ${SRC_BASE}/${_link} ${_link}
.endfor
.for _src in ${SRCS}
	@cd ${_src} && ${COPYTREE_SHARE} . ${WRKDIR}/${_src:S/${SRC_BASE}//1}
.endfor

do-build:
.if !defined(HAS_LIB9P)
	cd ${WRKSRC}/contrib/lib9p && ${MAKE}
.endif
.for _src in ${SRCS}
.  if exists(${_src}/Makefile)
	cd ${WRKSRC}/${_src:S/${SRC_BASE}//1} && ${MAKE} SRCTOP=${WRKSRC} SYSDIR=${SRC_BASE}/sys
.  endif
.endfor

do-install:
.if !defined(HAS_LIB9P)
.  for _lib in lib9p.so lib9p.so.1
	${INSTALL_LIB} ${WRKSRC}/contrib/lib9p/${_lib} ${STAGEDIR}${PREFIX}/lib
.  endfor
.endif
	${INSTALL_LIB} ${WRKSRC}/lib/libvmmapi/libvmmapi.so.5.1 ${STAGEDIR}${PREFIX}/lib
.for _bin in bhyve bhyveload bhyvectl
	${INSTALL_PROGRAM} ${WRKSRC}/usr.sbin/${_bin}/${_bin} ${STAGEDIR}${PREFIX}/sbin
	${INSTALL_MAN} ${WRKSRC}/usr.sbin/${_bin}/${_bin}.8.gz ${STAGEDIR}${PREFIX}/man/man8
.endfor
	${INSTALL_PROGRAM} ${WRKSRC}/sys/modules/vmm/vmm.ko ${STAGEDIR}${KMODDIR}

.include <bsd.port.mk>

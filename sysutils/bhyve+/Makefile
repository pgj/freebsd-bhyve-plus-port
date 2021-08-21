PORTNAME=	bhyve+
PORTVERSION=	0.1.0
CATEGORIES=	sysutils

MAINTAINER=	pali.gabor@gmail.com
COMMENT=	BHyVe with unofficial extensions

LICENSE=	BSD2CLAUSE

.include <bsd.port.options.mk>

BHYVE_SRC=	${SRC_BASE}/usr.sbin/bhyve

.if !exists(${BHYVE_SRC})
IGNORE=		requires bhyve source files in ${BHYVE_SRC}
.endif

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

post-extract:
.if !defined(HAS_LIB9P)
	cd ${WRKSRC} && ${LN} -s ${GH_PROJECT}-${GH_TAGNAME} lib9p
.endif
	cd ${BHYVE_SRC} && ${COPYTREE_SHARE} . ${WRKDIR}/bhyve/

do-build:
.if !defined(HAS_LIB9P)
	cd ${WRKSRC}/lib9p && ${MAKE} -m ${SRC_BASE}/share/mk
.endif
	cd ${WRKSRC}/bhyve && ${MAKE} -m ${SRC_BASE}/share/mk

do-install:
.if !defined(HAS_LIB9P)
.for _lib in lib9p.so lib9p.so.1
	${INSTALL_LIB} ${WRKSRC}/lib9p/${_lib} ${STAGEDIR}${PREFIX}/lib
.endfor
.endif
	${INSTALL_PROGRAM} ${WRKSRC}/bhyve/bhyve ${STAGEDIR}${PREFIX}/sbin
	${INSTALL_MAN} ${WRKSRC}/bhyve/bhyve.8.gz ${STAGEDIR}${PREFIX}/man/man8

.include <bsd.port.mk>

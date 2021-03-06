#	$OpenBSD: Makefile,v 1.54 2010/06/27 19:19:56 phessler Exp $

REGRESS_TARGETS=	t1 t2 t3 t4 t5 t6 t7 t-exec
tests:		$(REGRESS_TARGETS)

# Interop tests are not run by default
interop interop-tests: t-exec-interop

clean:
	for F in $(CLEANFILES); do rm -f $(OBJ)$$F; done
	rm -rf $(OBJ).putty

distclean:	clean

LTESTS= 	connect \
		proxy-connect \
		connect-privsep \
		proto-version \
		proto-mismatch \
		exit-status \
		envpass \
		transfer \
		banner \
		rekey \
		stderr-data \
		stderr-after-eof \
		broken-pipe \
		try-ciphers \
		yes-head \
		login-timeout \
		agent \
		agent-getpeereid \
		agent-timeout \
		agent-ptrace \
		keyscan \
		keygen-change \
		keygen-convert \
		key-options \
		scp \
		sftp \
		sftp-cmds \
		sftp-badcmds \
		sftp-batch \
		sftp-glob \
		reconfigure \
		dynamic-forward \
		forwarding \
		multiplex \
		reexec \
		brokenkeys \
		cfgmatch \
		addrmatch \
		localcommand \
		forcecommand \
		portnum \
		cert-hostkey \
		cert-userkey

INTEROP_TESTS=	putty-transfer putty-ciphers putty-kex conch-ciphers
#INTEROP_TESTS+=ssh-com ssh-com-client ssh-com-keygen ssh-com-sftp

USER!=		id -un
CLEANFILES=	t2.out t6.out1 t6.out2 t7.out t7.out.pub copy.1 copy.2 \
		authorized_keys_${USER} known_hosts pidfile \
		ssh_config sshd_config.orig ssh_proxy sshd_config sshd_proxy \
		rsa.pub rsa rsa1.pub rsa1 host.rsa host.rsa1 \
		rsa-agent rsa-agent.pub rsa1-agent rsa1-agent.pub \
		ls.copy banner.in banner.out empty.in \
		scp-ssh-wrapper.scp ssh_proxy_envpass remote_pid \
		sshd_proxy_bak rsa_ssh2_cr.prv rsa_ssh2_crnl.prv \
		known_hosts-cert host_ca_key* cert_host_key* \
		putty.rsa2 sshd_proxy_orig \
		authorized_principals_${USER}

# Enable all malloc(3) randomisations and checks
TEST_ENV=      "MALLOC_OPTIONS=AFGJPRX"

t1:
	ssh-keygen -if ${.CURDIR}/rsa_ssh2.prv | diff - ${.CURDIR}/rsa_openssh.prv

t2:
	cat ${.CURDIR}/rsa_openssh.prv > $(OBJ)/t2.out
	chmod 600 $(OBJ)/t2.out
	ssh-keygen -yf $(OBJ)/t2.out | diff - ${.CURDIR}/rsa_openssh.pub

t3:
	ssh-keygen -ef ${.CURDIR}/rsa_openssh.pub >$(OBJ)/rsa_secsh.pub
	ssh-keygen -if $(OBJ)/rsa_secsh.pub | diff - ${.CURDIR}/rsa_openssh.pub
	rm -f ${.CURDIR}/rsa_secsh.pub

t4:
	ssh-keygen -lf ${.CURDIR}/rsa_openssh.pub |\
		awk '{print $$2}' | diff - ${.CURDIR}/t4.ok

t5:
	ssh-keygen -Bf ${.CURDIR}/rsa_openssh.pub |\
		awk '{print $$2}' | diff - ${.CURDIR}/t5.ok

t6:
	ssh-keygen -if ${.CURDIR}/dsa_ssh2.prv > $(OBJ)/t6.out1
	ssh-keygen -if ${.CURDIR}/dsa_ssh2.pub > $(OBJ)/t6.out2
	chmod 600 $(OBJ)/t6.out1
	ssh-keygen -yf $(OBJ)/t6.out1 | diff - $(OBJ)/t6.out2

$(OBJ)/t7.out:
	ssh-keygen -q -t rsa -N '' -f $@

t7: $(OBJ)/t7.out
	ssh-keygen -lf $(OBJ)/t7.out > /dev/null
	ssh-keygen -Bf $(OBJ)/t7.out > /dev/null

t-exec:	${LTESTS:=.sh}
	@if [ "x$?" = "x" ]; then exit 0; fi; \
	for TEST in ""$?; do \
		echo "run test $${TEST}" ... 1>&2; \
		(env SUDO="${SUDO}" TEST_ENV=${TEST_ENV} sh ${.CURDIR}/test-exec.sh ${.OBJDIR} ${.CURDIR}/$${TEST}) || exit $$?; \
	done

t-exec-interop:	${INTEROP_TESTS:=.sh}
	@if [ "x$?" = "x" ]; then exit 0; fi; \
	for TEST in ""$?; do \
		echo "run test $${TEST}" ... 1>&2; \
		(env SUDO="${SUDO}" TEST_ENV=${TEST_ENV} sh ${.CURDIR}/test-exec.sh ${.OBJDIR} ${.CURDIR}/$${TEST}) || exit $$?; \
	done


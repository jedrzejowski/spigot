# Maintainer: Gordian Edenhofer <gordian.edenhofer@gmail.com>
# Submitter: Schala Zeal <schalaalexiazeal@gmail.com>

pkgname=spigot
_pkgver=1.13.2
_build=93
pkgver="${_pkgver}+b${_build}"
pkgrel=1
pkgdesc="High performance Minecraft server implementation"
arch=('any')
url="https://www.spigotmc.org/"
license=("LGPL")
depends=("java-runtime-headless>=8" 'screen' 'sudo' 'fontconfig' 'bash' 'awk' 'sed')
optdepends=("tar: needed in order to create world backups"
	"netcat: required in order to suspend an idle server")
makedepends=("java-environment>=8" 'git')
provides=("minecraft-server=${_pkgver%_*}" "bukkit=${_pkgver%_*}" "craftbukkit=${_pkgver%_*}")
conflicts=("bukkit" "craftbukkit" "spigot-patcher")
backup=("etc/conf.d/${pkgname}")
install="${pkgname}.install"
source=("BuildTools-${_pkgver}+b${_build}.jar::https://hub.spigotmc.org/jenkins/job/BuildTools/${_build}/artifact/target/BuildTools.jar"
	"${pkgname}-backup.service"
	"${pkgname}-backup.timer"
	"${pkgname}.service"
	"${pkgname}.conf"
	"${pkgname}.sh")
sha512sums=('f559fa53c0465d30a0165b643e01a7ddb576282c42d18b899bf844651c4fa144a5e7a73f1b168942834ee473cc58f7d41bf403074f9c39a763c318a00785228b'
            '914d079718bcf4adbe60ec1414ae95220be9e0ba6da8135d13fc9f1f82c7a5f1fb1844764a9d827bb9583bee2f6c10111880d0bcba135ec61d63b53a3f2aab27'
            '76c77e47c442b477216e968db2213612579b24add54cf0e0512f808498673500b4d24e59bce70b1e7479d724a9a897ceb154e937b88a476beb11c8776258b36c'
            '5a32439ff4b8fa9db89e9242206cf99109e0b00f29f87711c25342dda522171d999f7e18fb2013437ddf62cfec05b6677601933233aaf42bcb5d67eb7a1469ee'
            '33f456fd945bb2cfa6b390ce0ab02753cc6366e39abff80a4f2b7aa3aebe3cd31d148b785cbc2aa159dd8ad9fb03233a09f8693eb031b6b9db8dc03643d2397b'
            '58c48e7f06bc4508ca4ef2f661efcec3ab7aea56fd6cc46e1a9a33a99ea978e39912105b74fd359160b25a65195cd4812d8055501d97fc8a8f80ce388ee21a98')

_game="spigot"
_server_root="/srv/craftbukkit"

build() {
	export MAVEN_OPTS="-Xmx2g"
	java -jar "BuildTools-${_pkgver}+b${_build}.jar" --rev "${_pkgver}"
}

package() {
	install -Dm644 "${_game}.conf" "${pkgdir}/etc/conf.d/${_game}"
	install -Dm755 "${_game}.sh" "${pkgdir}/usr/bin/${_game}"
	install -Dm644 "${_game}.service" "${pkgdir}/usr/lib/systemd/system/${_game}.service"
	install -Dm644 "${_game}-backup.service" "${pkgdir}/usr/lib/systemd/system/${_game}-backup.service"
	install -Dm644 "${_game}-backup.timer" "${pkgdir}/usr/lib/systemd/system/${_game}-backup.timer"
	install -Dm644 "${_game}-${_pkgver}.jar" "${pkgdir}${_server_root}/${_game}.${_pkgver}.jar"
	ln -s "${_game}.${_pkgver}.jar" "${pkgdir}${_server_root}/${_game}.jar"

	# Link the log files
	mkdir -p "${pkgdir}/var/log/"
	install -dm775 "${pkgdir}/${_server_root}/logs"
	ln -s "${_server_root}/logs" "${pkgdir}/var/log/${_game}"

	# Give the group write permissions and set user or group ID on execution
	chmod g+ws "${pkgdir}${_server_root}"

	# Make plugins folder ready for drag and drop
	install -dm777 "${pkgdir}/${_server_root}/plugins"
}

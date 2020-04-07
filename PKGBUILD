# Maintainer: Gordian Edenhofer <gordian.edenhofer@gmail.com>
# Submitter: Schala Zeal <schalaalexiazeal@gmail.com>

_games=('-survival' '-creative')

pkgname=spigot
_pkgver=1.15.2
_build=108
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
sha512sums=('9982554fbdadf8dab00c7cdafe312d124d0be1ffdc56b56fcc450e2bbecd5e863cadeb48f4f9add01b24e27a58693c2bca5015a285044579fc5f22883047a9d9'
            '03ba1032b687553831021cfb0bed489e6301d0446c3b4f56d989d203855952911f3b1caaa00596f4731060ddf6684f66c3d674f3c5546b600cbe6585fc8560fb'
            '76c77e47c442b477216e968db2213612579b24add54cf0e0512f808498673500b4d24e59bce70b1e7479d724a9a897ceb154e937b88a476beb11c8776258b36c'
            '5a32439ff4b8fa9db89e9242206cf99109e0b00f29f87711c25342dda522171d999f7e18fb2013437ddf62cfec05b6677601933233aaf42bcb5d67eb7a1469ee'
            '33f456fd945bb2cfa6b390ce0ab02753cc6366e39abff80a4f2b7aa3aebe3cd31d148b785cbc2aa159dd8ad9fb03233a09f8693eb031b6b9db8dc03643d2397b'
            '829f659f1c8bad080128718248ffc5ad662b4dd8fe328fdb3a9637e2daebb11404afeef0fd2ac9bdfc39b3ea747a099426aa8373cf2744b3c4031eaa375477e2')

build() {
	export MAVEN_OPTS="-Xmx2g"
	java -jar "BuildTools-${_pkgver}+b${_build}.jar" --rev "${_pkgver}"
}

package() {

	base_game="spigot"
	base_server_root="/srv/craftbukkit"

	for suffix in "${_games[@]}"
	do
		echo "package: ${suffix}"
		my_game="${base_game}${suffix}"
		my_server_root="${base_server_root}${suffix}"

		instal_and_patch_file(){
			src=$1
			dst=$2
			install -Dm644 "${src}" "${dst}"
			sed -i "s+${base_game}+${my_game}+g" -i "${dst}"
			sed -i "s+${base_server_root}+${my_server_root}+g" -i "${dst}"
		}

		instal_and_patch_file "${pkgname}.conf" "${pkgdir}/etc/conf.d/${my_game}"
		instal_and_patch_file "${pkgname}.sh" "${pkgdir}/usr/bin/${my_game}"
		chmod +x "${pkgdir}/usr/bin/${my_game}"
		instal_and_patch_file "${pkgname}.service" "${pkgdir}/usr/lib/systemd/system/${my_game}.service"
		instal_and_patch_file "${pkgname}-backup.service" "${pkgdir}/usr/lib/systemd/system/${my_game}-backup.service"
		instal_and_patch_file "${pkgname}-backup.timer" "${pkgdir}/usr/lib/systemd/system/${my_game}-backup.timer"
		install -Dm644 "${pkgname}-${_pkgver}.jar" "${pkgdir}${my_server_root}/${pkgname}.${_pkgver}.jar"
		ln -s "${pkgname}.${_pkgver}.jar" "${pkgdir}${my_server_root}/${my_game}.jar"

		# Link the log files
		mkdir -p "${pkgdir}/var/log/"
		install -dm775 "${pkgdir}/${my_server_root}/logs"
		ln -s "${my_server_root}/logs" "${pkgdir}/var/log/${my_game}"

		# Give the group write permissions and set user or group ID on execution
		chmod g+ws "${pkgdir}${my_server_root}"

		# Make plugins folder ready for drag and drop
		install -dm777 "${pkgdir}/${my_server_root}/plugins"

	done

}

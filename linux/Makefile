#
# Quake2 Makefile for Linux 2.0
# 64-bit version
#
# Nov '97 by Zoid <zoid@idsoftware.com>
# Jan '19 by Krzysztof Kondrak <krzysztof.kondrak@gmail.com>
#
# ELF only
#

VERSION=3.21
VERSION_FN=$(VERSION)$(GLIBC)
RPM_RELEASE=6

ifneq (,$(findstring libc6,$(shell if [ -e /lib/libc.so.6* ];then echo libc6;fi)))
GLIBC=-glibc
else
GLIBC=
endif

KERNEL_ARCH=$(shell uname -m)

ifeq ($(KERNEL_ARCH),x86_64)
  ARCH=x64
  RPMARCH=i386
else ifeq ($(KERNEL_ARCH),amd64)
  ARCH=x64
  RPMARCH=i386
else
  $(error "Unsupported ARCH: ${KERNEL_ARCH}")
endif

NOARCH=noarch

MOUNT_DIR=..

BUILD_DEBUG_DIR=debug$(ARCH)$(GLIBC)
BUILD_RELEASE_DIR=release$(ARCH)$(GLIBC)
CLIENT_DIR=$(MOUNT_DIR)/client
SERVER_DIR=$(MOUNT_DIR)/server
REF_VK_DIR=$(MOUNT_DIR)/ref_vk
COMMON_DIR=$(MOUNT_DIR)/qcommon
LINUX_DIR=$(MOUNT_DIR)/linux
GAME_DIR=$(MOUNT_DIR)/game

CC=gcc
BASE_CFLAGS=-I/usr/local/include -Dstricmp=strcasecmp -D_GNU_SOURCE -Wno-format-truncation -Wno-unused-result
RELEASE_CFLAGS=$(BASE_CFLAGS) -DNDEBUG -O3 -funroll-loops \
	-fomit-frame-pointer -fexpensive-optimizations
DEBUG_CFLAGS=$(BASE_CFLAGS) -g -D_DEBUG
LDFLAGS=-L/usr/local/lib -ldl -lm -lasound -lpthread

VKLDFLAGS=-L/usr/X11R6/lib -L$(VULKAN_SDK)/lib -L/usr/local/lib -lX11 -lXext -lXxf86dga -lXxf86vm -lm -lvulkan -lstdc++
VKCFLAGS=-I$(VULKAN_SDK)/include

SHLIBEXT=so

SHLIBCFLAGS=-fPIC -std=c99
SHLIBCPPFLAGS=-fPIC -std=c++11
SHLIBLDFLAGS=-shared

DO_CC=$(CC) $(CFLAGS) -o $@ -c $<
DO_O_CC=$(CC) $(CFLAGS) -O -o $@ -c $<
DO_SHLIB_CC=$(CC) $(CFLAGS) $(SHLIBCFLAGS) -o $@ -c $<
DO_SHLIB_O_CC=$(CC) $(CFLAGS) -O $(SHLIBCFLAGS) -o $@ -c $<
DO_VK_SHLIB_CC=$(CC) $(CFLAGS) $(SHLIBCFLAGS) $(VKCFLAGS) -o $@ -c $<
DO_VK_SHLIB_CPP=$(CC) $(CFLAGS) $(SHLIBCPPFLAGS) $(VKCFLAGS) -o $@ -c $<
DO_VK_SHLIB_O_CC=$(CC) $(CFLAGS) -O $(SHLIBCFLAGS) $(VKCFLAGS) -o $@ -c $<
DO_AS=$(CC) $(CFLAGS) -DELF -x assembler-with-cpp -o $@ -c $<
DO_SHLIB_AS=$(CC) $(CFLAGS) $(SHLIBCFLAGS) -DELF -x assembler-with-cpp -o $@ -c $<

#############################################################################
# SETUP AND BUILD
#############################################################################

TARGETS=$(BUILDDIR)/quake2 \
	$(BUILDDIR)/baseq2/game$(ARCH).$(SHLIBEXT) \
	$(BUILDDIR)/ref_vk.$(SHLIBEXT)

build_debug:
	@-mkdir -p $(BUILD_DEBUG_DIR) \
		$(BUILD_DEBUG_DIR)/baseq2 \
		$(BUILD_DEBUG_DIR)/client \
		$(BUILD_DEBUG_DIR)/ref_vk \
		$(BUILD_DEBUG_DIR)/game
	$(MAKE) targets BUILDDIR=$(BUILD_DEBUG_DIR) CFLAGS="$(DEBUG_CFLAGS)"

build_release:
	@-mkdir -p $(BUILD_RELEASE_DIR) \
		$(BUILD_RELEASE_DIR)/baseq2 \
		$(BUILD_RELEASE_DIR)/client \
		$(BUILD_RELEASE_DIR)/ref_vk \
		$(BUILD_RELEASE_DIR)/game
	$(MAKE) targets BUILDDIR=$(BUILD_RELEASE_DIR) CFLAGS="$(RELEASE_CFLAGS)"

all: build_debug build_release
debug: build_debug
release: build_release

targets: $(TARGETS)

#############################################################################
# CLIENT/SERVER
#############################################################################

QUAKE2_OBJS = \
	$(BUILDDIR)/client/cl_cin.o \
	$(BUILDDIR)/client/cl_ents.o \
	$(BUILDDIR)/client/cl_fx.o \
	$(BUILDDIR)/client/cl_input.o \
	$(BUILDDIR)/client/cl_inv.o \
	$(BUILDDIR)/client/cl_main.o \
	$(BUILDDIR)/client/cl_newfx.o \
	$(BUILDDIR)/client/cl_parse.o \
	$(BUILDDIR)/client/cl_pred.o \
	$(BUILDDIR)/client/cl_tent.o \
	$(BUILDDIR)/client/cl_scrn.o \
	$(BUILDDIR)/client/cl_view.o \
	$(BUILDDIR)/client/console.o \
	$(BUILDDIR)/client/keys.o \
	$(BUILDDIR)/client/menu.o \
	$(BUILDDIR)/client/snd_dma.o \
	$(BUILDDIR)/client/snd_mem.o \
	$(BUILDDIR)/client/snd_miniaudio.o \
	$(BUILDDIR)/client/snd_mix.o \
	$(BUILDDIR)/client/qmenu.o \
	$(BUILDDIR)/client/m_flash.o \
	\
	$(BUILDDIR)/client/cmd.o \
	$(BUILDDIR)/client/cmodel.o \
	$(BUILDDIR)/client/common.o \
	$(BUILDDIR)/client/crc.o \
	$(BUILDDIR)/client/cvar.o \
	$(BUILDDIR)/client/files.o \
	$(BUILDDIR)/client/md4.o \
	$(BUILDDIR)/client/net_chan.o \
	\
	$(BUILDDIR)/client/sv_ccmds.o \
	$(BUILDDIR)/client/sv_ents.o \
	$(BUILDDIR)/client/sv_game.o \
	$(BUILDDIR)/client/sv_init.o \
	$(BUILDDIR)/client/sv_main.o \
	$(BUILDDIR)/client/sv_send.o \
	$(BUILDDIR)/client/sv_user.o \
	$(BUILDDIR)/client/sv_world.o \
	\
	$(BUILDDIR)/client/cd_linux.o \
	$(BUILDDIR)/client/q_shlinux.o \
	$(BUILDDIR)/client/vid_menu.o \
	$(BUILDDIR)/client/vid_so.o \
	$(BUILDDIR)/client/snd_alsa.o \
	$(BUILDDIR)/client/sys_linux.o \
	$(BUILDDIR)/client/glob.o \
	$(BUILDDIR)/client/net_udp.o \
	\
	$(BUILDDIR)/client/q_shared.o \
	$(BUILDDIR)/client/pmove.o

QUAKE2_AS_OBJS = \
	$(BUILDDIR)/client/snd_mixa.o

$(BUILDDIR)/quake2 : $(QUAKE2_OBJS) $(QUAKE2_AS_OBJS)
	$(CC) $(CFLAGS) -o $@ $(QUAKE2_OBJS) $(QUAKE2_AS_OBJS) $(LDFLAGS)

$(BUILDDIR)/client/cl_cin.o :     $(CLIENT_DIR)/cl_cin.c
	$(DO_CC)

$(BUILDDIR)/client/cl_ents.o :    $(CLIENT_DIR)/cl_ents.c
	$(DO_CC)

$(BUILDDIR)/client/cl_fx.o :      $(CLIENT_DIR)/cl_fx.c
	$(DO_CC)

$(BUILDDIR)/client/cl_input.o :   $(CLIENT_DIR)/cl_input.c
	$(DO_CC)

$(BUILDDIR)/client/cl_inv.o :     $(CLIENT_DIR)/cl_inv.c
	$(DO_CC)

$(BUILDDIR)/client/cl_main.o :    $(CLIENT_DIR)/cl_main.c
	$(DO_CC)

$(BUILDDIR)/client/cl_newfx.o :    $(CLIENT_DIR)/cl_newfx.c
	$(DO_CC)

$(BUILDDIR)/client/cl_parse.o :   $(CLIENT_DIR)/cl_parse.c
	$(DO_CC)

$(BUILDDIR)/client/cl_pred.o :    $(CLIENT_DIR)/cl_pred.c
	$(DO_CC)

$(BUILDDIR)/client/cl_tent.o :    $(CLIENT_DIR)/cl_tent.c
	$(DO_CC)

$(BUILDDIR)/client/cl_scrn.o :    $(CLIENT_DIR)/cl_scrn.c
	$(DO_CC)

$(BUILDDIR)/client/cl_view.o :    $(CLIENT_DIR)/cl_view.c
	$(DO_CC)

$(BUILDDIR)/client/console.o :    $(CLIENT_DIR)/console.c
	$(DO_CC)

$(BUILDDIR)/client/keys.o :       $(CLIENT_DIR)/keys.c
	$(DO_CC)

$(BUILDDIR)/client/menu.o :       $(CLIENT_DIR)/menu.c
	$(DO_CC)

$(BUILDDIR)/client/snd_dma.o :    $(CLIENT_DIR)/snd_dma.c
	$(DO_CC)

$(BUILDDIR)/client/snd_mem.o :    $(CLIENT_DIR)/snd_mem.c
	$(DO_CC)

$(BUILDDIR)/client/snd_miniaudio.o : $(CLIENT_DIR)/snd_miniaudio.c
	$(DO_CC)

$(BUILDDIR)/client/snd_mix.o :    $(CLIENT_DIR)/snd_mix.c
	$(DO_CC)

$(BUILDDIR)/client/qmenu.o :      $(CLIENT_DIR)/qmenu.c
	$(DO_CC)

$(BUILDDIR)/client/m_flash.o :    $(GAME_DIR)/m_flash.c
	$(DO_CC)

$(BUILDDIR)/client/cmd.o :        $(COMMON_DIR)/cmd.c
	$(DO_CC)

$(BUILDDIR)/client/cmodel.o :     $(COMMON_DIR)/cmodel.c
	$(DO_CC)

$(BUILDDIR)/client/common.o :     $(COMMON_DIR)/common.c
	$(DO_CC)

$(BUILDDIR)/client/crc.o :        $(COMMON_DIR)/crc.c
	$(DO_CC)

$(BUILDDIR)/client/cvar.o :       $(COMMON_DIR)/cvar.c
	$(DO_CC)

$(BUILDDIR)/client/files.o :      $(COMMON_DIR)/files.c
	$(DO_CC)

$(BUILDDIR)/client/md4.o :        $(COMMON_DIR)/md4.c
	$(DO_CC)

$(BUILDDIR)/client/net_chan.o :   $(COMMON_DIR)/net_chan.c
	$(DO_CC)

$(BUILDDIR)/client/q_shared.o :   $(GAME_DIR)/q_shared.c
	$(DO_CC)

$(BUILDDIR)/client/pmove.o :      $(COMMON_DIR)/pmove.c
	$(DO_CC)

$(BUILDDIR)/client/sv_ccmds.o :   $(SERVER_DIR)/sv_ccmds.c
	$(DO_CC)

$(BUILDDIR)/client/sv_ents.o :    $(SERVER_DIR)/sv_ents.c
	$(DO_CC)

$(BUILDDIR)/client/sv_game.o :    $(SERVER_DIR)/sv_game.c
	$(DO_CC)

$(BUILDDIR)/client/sv_init.o :    $(SERVER_DIR)/sv_init.c
	$(DO_CC)

$(BUILDDIR)/client/sv_main.o :    $(SERVER_DIR)/sv_main.c
	$(DO_CC)

$(BUILDDIR)/client/sv_send.o :    $(SERVER_DIR)/sv_send.c
	$(DO_CC)

$(BUILDDIR)/client/sv_user.o :    $(SERVER_DIR)/sv_user.c
	$(DO_CC)

$(BUILDDIR)/client/sv_world.o :   $(SERVER_DIR)/sv_world.c
	$(DO_CC)

$(BUILDDIR)/client/cd_linux.o :   $(LINUX_DIR)/cd_linux.c
	$(DO_CC)

$(BUILDDIR)/client/q_shlinux.o :  $(LINUX_DIR)/q_shlinux.c
	$(DO_O_CC)

$(BUILDDIR)/client/vid_menu.o :   $(LINUX_DIR)/vid_menu.c
	$(DO_CC)

$(BUILDDIR)/client/vid_so.o :     $(LINUX_DIR)/vid_so.c
	$(DO_CC)

$(BUILDDIR)/client/snd_alsa.o :  $(LINUX_DIR)/snd_alsa.c
	$(DO_CC)

$(BUILDDIR)/client/snd_mixa.o :   $(LINUX_DIR)/snd_mixa.s
	$(DO_AS)

$(BUILDDIR)/client/sys_linux.o :  $(LINUX_DIR)/sys_linux.c
	$(DO_CC)

$(BUILDDIR)/client/glob.o :       $(LINUX_DIR)/glob.c
	$(DO_CC)

$(BUILDDIR)/client/net_udp.o :    $(LINUX_DIR)/net_udp.c
	$(DO_CC)

#############################################################################
# GAME
#############################################################################

GAME_OBJS = \
	$(BUILDDIR)/game/q_shared.o \
	$(BUILDDIR)/game/g_ai.o \
	$(BUILDDIR)/game/p_client.o \
	$(BUILDDIR)/game/g_cmds.o \
	$(BUILDDIR)/game/g_svcmds.o \
	$(BUILDDIR)/game/g_chase.o \
	$(BUILDDIR)/game/g_combat.o \
	$(BUILDDIR)/game/g_func.o \
	$(BUILDDIR)/game/g_items.o \
	$(BUILDDIR)/game/g_main.o \
	$(BUILDDIR)/game/g_misc.o \
	$(BUILDDIR)/game/g_monster.o \
	$(BUILDDIR)/game/g_phys.o \
	$(BUILDDIR)/game/g_save.o \
	$(BUILDDIR)/game/g_spawn.o \
	$(BUILDDIR)/game/g_target.o \
	$(BUILDDIR)/game/g_trigger.o \
	$(BUILDDIR)/game/g_turret.o \
	$(BUILDDIR)/game/g_utils.o \
	$(BUILDDIR)/game/g_weapon.o \
	$(BUILDDIR)/game/m_actor.o \
	$(BUILDDIR)/game/m_berserk.o \
	$(BUILDDIR)/game/m_boss2.o \
	$(BUILDDIR)/game/m_boss3.o \
	$(BUILDDIR)/game/m_boss31.o \
	$(BUILDDIR)/game/m_boss32.o \
	$(BUILDDIR)/game/m_brain.o \
	$(BUILDDIR)/game/m_chick.o \
	$(BUILDDIR)/game/m_flipper.o \
	$(BUILDDIR)/game/m_float.o \
	$(BUILDDIR)/game/m_flyer.o \
	$(BUILDDIR)/game/m_gladiator.o \
	$(BUILDDIR)/game/m_gunner.o \
	$(BUILDDIR)/game/m_hover.o \
	$(BUILDDIR)/game/m_infantry.o \
	$(BUILDDIR)/game/m_insane.o \
	$(BUILDDIR)/game/m_medic.o \
	$(BUILDDIR)/game/m_move.o \
	$(BUILDDIR)/game/m_mutant.o \
	$(BUILDDIR)/game/m_parasite.o \
	$(BUILDDIR)/game/m_soldier.o \
	$(BUILDDIR)/game/m_supertank.o \
	$(BUILDDIR)/game/m_tank.o \
	$(BUILDDIR)/game/p_hud.o \
	$(BUILDDIR)/game/p_trail.o \
	$(BUILDDIR)/game/p_view.o \
	$(BUILDDIR)/game/p_weapon.o \
	$(BUILDDIR)/game/m_flash.o

$(BUILDDIR)/baseq2/game$(ARCH).$(SHLIBEXT) : $(GAME_OBJS)
	$(CC) $(CFLAGS) $(SHLIBLDFLAGS) -o $@ $(GAME_OBJS)

$(BUILDDIR)/game/g_ai.o :        $(GAME_DIR)/g_ai.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/p_client.o :    $(GAME_DIR)/p_client.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_cmds.o :      $(GAME_DIR)/g_cmds.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_svcmds.o :    $(GAME_DIR)/g_svcmds.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_chase.o :    $(GAME_DIR)/g_chase.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_combat.o :    $(GAME_DIR)/g_combat.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_func.o :      $(GAME_DIR)/g_func.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_items.o :     $(GAME_DIR)/g_items.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_main.o :      $(GAME_DIR)/g_main.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_misc.o :      $(GAME_DIR)/g_misc.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_monster.o :   $(GAME_DIR)/g_monster.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_phys.o :      $(GAME_DIR)/g_phys.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_save.o :      $(GAME_DIR)/g_save.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_spawn.o :     $(GAME_DIR)/g_spawn.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_target.o :    $(GAME_DIR)/g_target.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_trigger.o :   $(GAME_DIR)/g_trigger.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_turret.o :    $(GAME_DIR)/g_turret.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_utils.o :     $(GAME_DIR)/g_utils.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/g_weapon.o :    $(GAME_DIR)/g_weapon.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_actor.o :     $(GAME_DIR)/m_actor.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_berserk.o :   $(GAME_DIR)/m_berserk.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_boss2.o :     $(GAME_DIR)/m_boss2.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_boss3.o :     $(GAME_DIR)/m_boss3.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_boss31.o :     $(GAME_DIR)/m_boss31.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_boss32.o :     $(GAME_DIR)/m_boss32.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_brain.o :     $(GAME_DIR)/m_brain.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_chick.o :     $(GAME_DIR)/m_chick.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_flipper.o :   $(GAME_DIR)/m_flipper.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_float.o :     $(GAME_DIR)/m_float.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_flyer.o :     $(GAME_DIR)/m_flyer.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_gladiator.o : $(GAME_DIR)/m_gladiator.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_gunner.o :    $(GAME_DIR)/m_gunner.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_hover.o :     $(GAME_DIR)/m_hover.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_infantry.o :  $(GAME_DIR)/m_infantry.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_insane.o :    $(GAME_DIR)/m_insane.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_medic.o :     $(GAME_DIR)/m_medic.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_move.o :      $(GAME_DIR)/m_move.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_mutant.o :    $(GAME_DIR)/m_mutant.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_parasite.o :  $(GAME_DIR)/m_parasite.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_soldier.o :   $(GAME_DIR)/m_soldier.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_supertank.o : $(GAME_DIR)/m_supertank.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_tank.o :      $(GAME_DIR)/m_tank.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/p_hud.o :       $(GAME_DIR)/p_hud.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/p_trail.o :     $(GAME_DIR)/p_trail.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/p_view.o :      $(GAME_DIR)/p_view.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/p_weapon.o :    $(GAME_DIR)/p_weapon.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/q_shared.o :    $(GAME_DIR)/q_shared.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/game/m_flash.o :     $(GAME_DIR)/m_flash.c
	$(DO_SHLIB_CC)

#############################################################################
# REF_VK
#############################################################################

REF_VK_OBJS = \
	$(BUILDDIR)/ref_vk/vk_buffer.o \
	$(BUILDDIR)/ref_vk/vk_cmd.o \
	$(BUILDDIR)/ref_vk/vk_common.o \
	$(BUILDDIR)/ref_vk/vk_device.o \
	$(BUILDDIR)/ref_vk/vk_draw.o \
	$(BUILDDIR)/ref_vk/vk_image.o \
	$(BUILDDIR)/ref_vk/vk_imp.o \
	$(BUILDDIR)/ref_vk/vk_light.o \
	$(BUILDDIR)/ref_vk/vk_mem_alloc.o \
	$(BUILDDIR)/ref_vk/vk_mesh.o \
	$(BUILDDIR)/ref_vk/vk_model.o \
	$(BUILDDIR)/ref_vk/vk_pipeline.o \
	$(BUILDDIR)/ref_vk/vk_rmain.o \
	$(BUILDDIR)/ref_vk/vk_rmisc.o \
	$(BUILDDIR)/ref_vk/vk_rsurf.o \
	$(BUILDDIR)/ref_vk/vk_shaders.o \
	$(BUILDDIR)/ref_vk/vk_swapchain.o \
	$(BUILDDIR)/ref_vk/vk_validation.o \
	$(BUILDDIR)/ref_vk/vk_warp.o \
	\
	$(BUILDDIR)/ref_vk/q_shared.o \
	$(BUILDDIR)/ref_vk/q_shlinux.o \
	$(BUILDDIR)/ref_vk/glob.o

$(BUILDDIR)/ref_vk.$(SHLIBEXT) : $(REF_VK_OBJS)
	$(CC) $(CFLAGS) $(SHLIBLDFLAGS) -o $@ $(REF_VK_OBJS) $(VKLDFLAGS)

$(BUILDDIR)/ref_vk/vk_buffer.o :        $(REF_VK_DIR)/vk_buffer.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_cmd.o :       $(REF_VK_DIR)/vk_cmd.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_common.o :      $(REF_VK_DIR)/vk_common.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_device.o :       $(REF_VK_DIR)/vk_device.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_draw.o :        $(REF_VK_DIR)/vk_draw.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_image.o :       $(REF_VK_DIR)/vk_image.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_light.o :       $(REF_VK_DIR)/vk_light.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_mem_alloc.o :       $(REF_VK_DIR)/vk_mem_alloc.cpp
	$(DO_VK_SHLIB_CPP)

$(BUILDDIR)/ref_vk/vk_mesh.o :       $(REF_VK_DIR)/vk_mesh.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_model.o :        $(REF_VK_DIR)/vk_model.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_pipeline.o :        $(REF_VK_DIR)/vk_pipeline.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_rmain.o :        $(REF_VK_DIR)/vk_rmain.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_rmisc.o :        $(REF_VK_DIR)/vk_rmisc.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_rsurf.o :        $(REF_VK_DIR)/vk_rsurf.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_shaders.o :        $(REF_VK_DIR)/vk_shaders.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_swapchain.o :        $(REF_VK_DIR)/vk_swapchain.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_validation.o :        $(REF_VK_DIR)/vk_validation.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/vk_warp.o :        $(REF_VK_DIR)/vk_warp.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/q_shared.o :       $(GAME_DIR)/q_shared.c
	$(DO_SHLIB_CC)

$(BUILDDIR)/ref_vk/q_shlinux.o :      $(LINUX_DIR)/q_shlinux.c
	$(DO_VK_SHLIB_O_CC)

$(BUILDDIR)/ref_vk/vk_imp.o :       $(LINUX_DIR)/vk_imp.c
	$(DO_VK_SHLIB_CC)

$(BUILDDIR)/ref_vk/glob.o :           $(LINUX_DIR)/glob.c
	$(DO_VK_SHLIB_CC)


#############################################################################
# MISC
#############################################################################

clean: clean-debug clean-release

clean-debug:
	$(MAKE) clean2 BUILDDIR=$(BUILD_DEBUG_DIR) CFLAGS="$(DEBUG_CFLAGS)"

clean-release:
	$(MAKE) clean2 BUILDDIR=$(BUILD_RELEASE_DIR) CFLAGS="$(DEBUG_CFLAGS)"

clean2:
	-rm -f \
	$(QUAKE2_OBJS) \
	$(QUAKE2_AS_OBJS) \
	$(GAME_OBJS) \
	$(REF_VK_OBJS)


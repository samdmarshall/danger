# Warn when there is a big PR
warn('Big PR') if git.lines_of_code > @SDM_DANGER_BIG_PR_LINES

# Make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn('PR is classed as Work in Progress') if github.pr_title.include? '[WIP]'

# determine if any of the files were modified
def didModify(files_array)
	did_modify_files = false
	files_array.each do |file_name|
		if git.modified_files.include?(file_name) || git.deleted_files.include?(file_name)
			did_modify_files = true
		end
	end
	return did_modify_files
end

# Warn when the build system files see changes
warn('Changes to build files') if didModify(@SDM_DANGER_BUILD_FILES)

# Warn when changing the requirements files
warn('Changes to installation requirements files') if didModify(@SDM_DANGER_INSTALL_REQUIREMENTS_FILES)

# Fail if changes to immutable files, such as License or CoC
fail('Do not modify the license or Code of Conduct') if didModify(@SDM_DANGER_IMMUTABLE_FILES)

# put labels on PRs, this will autofail all PRs without contributor intervention (this is intentional to force someone to look at and categorize each PR before merging)
fail('PR needs labels', sticky: true) if github.pr_labels.empty?

# look up the build artifacts for circle ci, these should be posted to the PR
if @SDM_DANGER_REPORT_CIRCLE_CI_ARTIFACTS
	username = ENV['CIRCLE_PROJECT_USERNAME']
	project_name = ENV['CIRCLE_PROJECT_REPONAME']
	build_number = ENV['CIRCLE_BUILD_NUM']
	node_index = ENV['CIRCLE_NODE_INDEX']
	artifacts_path = ENV['CIRCLE_ARTIFACTS']
	should_display_message = username && project_name && build_number && node_index && artifacts_path
	if should_display_message

		# build the path to where the circle CI artifacts will be uploaded to
		circle_ci_artifact_path  = 'https://circleci.com/api/v1/project/'
		circle_ci_artifact_path += username
		circle_ci_artifact_path += '/'
		circle_ci_artifact_path += project_name
		circle_ci_artifact_path += '/'
		circle_ci_artifact_path += build_number
		circle_ci_artifact_path += '/artifacts/'
		circle_ci_artifact_path += node_index
		circle_ci_artifact_path += artifacts_path
		circle_ci_artifact_path += '/'

		message_string = ''
		@SDM_DANGER_REPORT_CIRCLE_CI_ARTIFACTS.each do |artifact|
			# create a markdown link that uses the message text and the artifact path
			message_string  = '['+artifact['message']+']'
			message_string += '('+circle_ci_artifact_path+artifact['path']+')'

			# if this isn't the last artifact then append the " & " string to make this one line
			message_string += ' & ' if @SDM_DANGER_REPORT_CIRCLE_CI_ARTIFACTS.last != artifact
		end
		message(message_string)
	end
end
